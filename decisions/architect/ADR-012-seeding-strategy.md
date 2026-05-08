# ADR-012 — Seeding Strategy

**Date:** 2026-05-08
**Status:** Approved
**Decider:** Architect

---

## Context

Trackwise is a multi-tenant application. Even an empty database needs **bootstrap data** for the app to be functional after deploy — at minimum, one organisation row and one Manager user (otherwise no one can log in). Beyond bootstrap, the team may want **demo data** for development and stakeholder demos (PM has indicated this is uncertain — they may bring an existing DB instead).

This ADR defines:
- What seed data exists
- How seeds are organised
- How seeds run per environment
- How re-running seeds behaves (idempotent, additive, destructive)

PM-confirmed scope (Session 8): **Tier 1 only** — bootstrap data. Demo data (Tier 2) is **not** implemented. If PM later requests demo data, this ADR is amended; the mechanism is already in place.

---

## Decision

### Three tiers (only Tier 1 implemented in v1.0)

| Tier | Purpose | Implemented in v1.0? |
|------|---------|----------------------|
| **Tier 1 — Bootstrap** | Required for the app to run | **Yes** |
| **Tier 2 — Demo** | Sample data for dev/stakeholder demos | **No** (deferred — PM may bring existing DB) |
| **Tier 3 — Migration** | Pulling data from an existing source | **No** (depends on Tier 2 decision; likely empty for greenfield) |

### Mechanism: NestJS standalone seed application

- Seeds live in `src/seeds/` within the NestJS backend repo.
- Run via `npm run seed:bootstrap` (Tier 1) — invokes a NestJS CommandModule that bootstraps the application context, runs the seed, exits.
- Each tier has its own command. Tiers can be run independently or composed (`npm run seed:all`).
- Implementation library: [`nestjs-command`](https://www.npmjs.com/package/nestjs-command) — adds `@Command` decorator for CLI commands inside NestJS.

### Idempotency

- Every seed operation **upserts** by a natural key, never blind-inserts.
- Re-running a seed against a populated database is safe — no duplicates, no errors.
- Natural keys per entity are documented in the seed file (e.g. `organisations.name`, `users.email`).

### Per-environment behaviour

| Environment | Tier 1 (bootstrap) | Tier 2 (demo) | Tier 3 (migration) |
|-------------|--------------------|--------------------|--------------------|
| `local` (dev) | On every fresh checkout | Manual opt-in | Never |
| `staging` | Once, on initial deploy | Manual opt-in | Manual opt-in |
| `production` | **Once, gated by env flag** | Never | Manual opt-in (one-time) |
| `test` (CI) | On every test run | On every test run that needs it | Never |

Production gating: `npm run seed:bootstrap` in production requires `TRACKWISE_PRODUCTION_SEED=true` env var. Without it, the command refuses to run. Belt-and-suspenders against accidental re-seed.

---

## Tier 1 — Bootstrap data (specification)

The minimum to make the app usable. Five inserts, total.

### B1. Single organisation

```
{
  "name": "Trackwise Default Org",
  "logo_url": null
}
```

Natural key: `name`. Re-runs upsert by name.

### B2. Single Manager user

The first Manager. Provisioned with a Cognito user during deployment.

```
{
  "org_id": <B1's _id>,
  "cognito_sub": <env-provided>,
  "full_name": "Trackwise Admin",
  "email": "admin@trackwise.local",  // overridable via env
  "role": "cto_ceo",
  "status": "active",
  "created_by_user_id": null
}
```

Natural key: `email` within `org_id`. Re-runs upsert by composite key.

**Cognito side:** the corresponding Cognito user is created out-of-band by the deployment script (Terraform / CDK / shell), not by this seed. This seed only writes the Mongo row — `cognito_sub` is supplied by the deployment as an env var.

If `cognito_sub` is not provided, the seed creates the row with a placeholder and logs a warning. Production gating prevents this from happening accidentally.

### B3. Mongo TTL indexes for cache collections

Strictly speaking this is schema bootstrap, not data. But TTL indexes don't auto-create from Mongoose `autoIndex` reliably across environments, so we explicitly create them:

```
db.github_cache_milestones.createIndex({ expires_at: 1 }, { expireAfterSeconds: 0 });
db.github_cache_sprint_membership.createIndex({ expires_at: 1 }, { expireAfterSeconds: 0 });
```

Idempotent — `createIndex` is a no-op if the index exists.

### B4. Mongo unique compound indexes

Schema-level uniqueness that Mongoose `unique: true` declares but doesn't always enforce on first deploy if the collection didn't exist:

```
db.users.createIndex({ org_id: 1, email: 1 }, { unique: true });
db.users.createIndex({ cognito_sub: 1 }, { unique: true });
db.cr_summaries.createIndex({ org_id: 1, project_id: 1 }, { unique: true });
db.sprint_annotations.createIndex({ org_id: 1, project_id: 1, sprint_number: 1 }, { unique: true });
db.github_cache_milestones.createIndex({ org_id: 1, project_id: 1 }, { unique: true });
db.github_cache_sprint_membership.createIndex({ org_id: 1, project_id: 1, sprint_label: 1 }, { unique: true });
```

Idempotent.

### B5. Audit log for B2

The Manager user creation must itself be audited:

```
{
  "org_id": <B1's _id>,
  "actor_user_id": null,  // system-generated
  "action": "user.create",
  "target_type": "user",
  "target_id": <B2's _id>,
  "before": null,
  "after": { "email": "admin@trackwise.local", "role": "cto_ceo", "status": "active" },
  "ip_address": null,
  "user_agent": "trackwise-seed/v1.0"
}
```

Natural key: combination of `target_id` + `action` (one row per first creation). Re-run skips if already present.

---

## Tier 2 — Demo data (specification, deferred)

Not implemented in v1.0. Documented here as a placeholder so future implementation has a starting point.

If PM requests demo data:
- Insert 2 sample clients
- Insert 4 sample projects across the clients (mix of `fixed_scope` and `fixed_budget`)
- Insert 5 sample employees with domain knowledge
- Insert ~20 sample CR records spanning all four origins, all decision states, varying hour values
- Insert sample sprint_annotations for the most recent 2-3 sprint numbers per project
- Trigger `cr_summaries` recomputation for each project after CR insert

Demo data is fully isolated behind `npm run seed:demo`. Production never runs it.

---

## Tier 3 — Migration data (specification, deferred)

Not implemented in v1.0. Trackwise is greenfield; nothing to migrate from.

If a future scenario requires data import:
- Source-specific scripts in `src/seeds/migration/<source>/`
- Each migration is one-time — re-runnability is "skip if data already exists" not "upsert"
- Each migration writes a row to a `migrations` collection (out of scope for this ADR) so we know what ran

---

## Operational rules

### Order of execution

Bootstrap order is fixed:

1. B3, B4 — index creation (must come before any insert)
2. B1 — organisation
3. B2 — Manager user
4. B5 — audit log

The seed runner enforces this — individual sub-commands cannot be run out of order in production. (In local/dev, the developer can override.)

### Failure handling

- Each step is a Mongo session-bound transaction where applicable.
- If any step fails, the seed exits non-zero and logs the failure context.
- Partial success is possible — re-running picks up from where it left off because every step is idempotent.

### Logging

- Every seed step logs:
  - `step_id` (e.g. `B2`)
  - `action` (`upsert` / `skip` / `created` / `error`)
  - Affected row count
  - Duration ms
- Logs go to stdout for local; CloudWatch in deployed envs.

### Secrets

- Cognito credentials, MongoDB connection string, encryption keys — sourced from AWS Secrets Manager in deployed envs, `.env.local` in dev.
- Seed code never hardcodes credentials.
- `cognito_sub` for the bootstrap Manager comes from the deployment pipeline (which created the Cognito user); seed receives it via env var.

---

## Deployment integration

### Initial deploy (production / staging)

Sequence:
1. CDK / Terraform creates AWS resources (Cognito User Pool, Lambda, API Gateway, Atlas cluster).
2. CDK / Terraform creates the bootstrap Cognito user and outputs the `cognito_sub`.
3. Pipeline runs `TRACKWISE_PRODUCTION_SEED=true COGNITO_SUB=<value> npm run seed:bootstrap` against the new database.
4. Health check verifies seeded state (B1 row exists, B2 row exists with expected `cognito_sub`).
5. App is now usable.

### Subsequent deploys

- Re-running `seed:bootstrap` is a no-op because every step is idempotent.
- Pipeline does not run seeds on regular deploys; only on initial deploy or explicit re-seed.
- Schema migrations (if any in future) are a separate mechanism — not handled by this seed framework.

### Local dev

- Developer runs `npm run seed:bootstrap` after a fresh `mongo` database. Default values (admin@trackwise.local, placeholder Cognito sub) are used.
- Developer can run `npm run seed:demo` if/when Tier 2 ships.

---

## Rationale

### NestJS CommandModule over standalone scripts
- Seed code shares the same dependency injection, schema definitions, and validators as the rest of the app.
- A standalone script would require duplicating Mongoose schema imports and the bootstrapping logic.
- `nestjs-command` is the idiomatic pattern for NestJS CLI tools.

### Idempotent upsert over destructive recreate
- Re-running seeds is a normal part of dev workflow — should be safe.
- Destructive reseed (drop + recreate) is dangerous in any non-dev environment; we don't want that to be the default.
- Idempotent code is also self-documenting — the natural key reads like a contract.

### Tier 1 only in v1.0
- PM has signalled they may bring an existing DB; demo data would be wasted effort.
- Bootstrap data is non-negotiable — the app cannot run without one user and one org.
- Tier 2 / Tier 3 are documented so future implementation has a starting point but no code is written speculatively.

### Production gating via env flag
- Re-running production seed accidentally would not corrupt data (it's idempotent), but it would still run unnecessary commands and pollute logs.
- The flag is a "you really mean it" signal that's hard to hit by accident.
- Compatible with all the major CI/CD systems' env var injection.

### `cognito_sub` from deployment, not seed
- Cognito user creation requires Cognito admin credentials — those belong to the deployment pipeline, not the application code.
- Seed only writes the application-side mirror of the Cognito user; Cognito itself is provisioned upstream.
- Decoupling lets us swap the auth provider without changing seed structure (theoretical — Cognito is the v1.0 choice).

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Mongo dump / restore | Doesn't handle Cognito user creation; not platform-portable; opaque to code review |
| Hand-written `db.runCommand` scripts | Loses NestJS schema validation; duplicates field definitions; no DI |
| ORM "fixtures" library (e.g. typeorm-fixtures) | Mongoose has no canonical fixture library; rolling NestJS commands gives more control |
| Shell scripts calling Mongo CLI | Cross-platform issues (Windows dev, Linux CI); no schema validation |
| Putting bootstrap inline in NestJS app startup (`onApplicationBootstrap`) | Couples bootstrap to runtime; can't run seed without starting the API |
| Skipping bootstrap data (require manual setup) | Friction for every new deploy and every dev clone; bootstrap is small enough to automate |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| Bootstrap user has placeholder Cognito sub if env not provided in dev | Local-only; production gating prevents this in real environments | Feedback from dev workflow |
| No schema migration framework (separate concern) | One ADR away when needed | First production schema change |
| Tier 2 / Tier 3 specs are sketches, not implementations | Documented above so the path is clear | PM decides demo data is needed |
| Seed runner has no rollback | Idempotent steps make rollback unnecessary; partial-state recovery is by re-run | A non-idempotent step is needed (it shouldn't be) |
| Seed code shares schema imports with app | Schema change affects seeds — must update both | Seed and app diverge; revisit module structure |

---

## Consequences

- A new `src/seeds/` folder structure is added to the backend repo.
- `nestjs-command` is added as a dev dependency.
- `package.json` gains scripts: `seed:bootstrap`, `seed:demo` (no-op stub for v1.0), `seed:all`.
- Deployment pipeline (DevOps role to define separately) gains a "run bootstrap seed" step on first deploy.
- DevOps should document the production gating env var (`TRACKWISE_PRODUCTION_SEED=true`) and the Cognito sub injection in the deployment runbook.
- A health-check endpoint `GET /health/bootstrap` returning whether B1 + B2 are present helps the deployment pipeline verify seed success — implementation deferred but the design slot is reserved.

---

## Review Trigger

Revisit this ADR when:
- PM requests Tier 2 demo data
- An existing DB is provided and Tier 3 migration is needed
- A second tenant is added — verify the bootstrap process scales (it should — no global state)
- A schema migration framework is needed — that ADR will clarify the boundary with this one
- The auth provider changes (Cognito → other) — `cognito_sub` field becomes provider-specific
