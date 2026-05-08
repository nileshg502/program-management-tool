# ADR-009 — Data Model: Core Entities

**Date:** 2026-05-08
**Status:** Approved
**Decider:** Architect

---

## Context

ADR-002 established the v1.0 collection list and the multi-tenancy model. This ADR specifies the **detailed schema** for the core entities — those that exist regardless of project activity:

- `organisations`
- `users` (new — formalised here per ADR-003 amendment)
- `clients`
- `projects`
- `employees`
- `domain_knowledge`
- `audit_logs` (new — formalised here per ADR-003 amendment)

Project-operational entities (sprints, milestones, cr_summaries, cr_records, sprint_annotations, alerts) are in [ADR-010](ADR-010-data-model-project-operations.md). GitHub cache collections are in [ADR-011](ADR-011-github-mirror-cache.md). Seeding is in [ADR-012](ADR-012-seeding-strategy.md).

---

## Decision — global conventions

These rules apply to every collection in this ADR (and downstream ADRs unless overridden).

### Field naming
- **snake_case** on all stored fields and on all API payloads (per `api-contract/CONVENTIONS.md`).
- Mongoose schema definitions use `@Prop({ name: 'snake_field' })` to map TypeScript camelCase properties to snake_case storage where needed; preferred approach is to use snake_case property names directly to avoid translation overhead.

### Identifiers
- Primary key: `_id` (MongoDB ObjectId), serialised in API responses as `id` (string).
- Foreign keys: stored as ObjectId, serialised as `<entity>_id` (e.g. `client_id`, `project_id`).
- Cognito subject: `cognito_sub` (string) — used to find the application user from a JWT.

### Tenancy
- Every document carries `org_id` (ObjectId) — required, indexed, leading field of every compound index.
- Every NestJS service method takes `org_id` from the request context (injected by `JwtAuthGuard`) and applies it as a filter — no exceptions.

### Audit fields
- Every collection in this ADR carries:
  - `created_at` (Date) — set by Mongoose `timestamps: true`
  - `updated_at` (Date) — set by Mongoose `timestamps: true`
  - `created_by_user_id` (ObjectId, nullable) — null for seed data; set for user-created records

### Soft delete vs hard delete
- **Hard delete** is the default for v1.0 — `archived_at` field is used only where an archive lifecycle is explicitly required (e.g. `clients`, `projects` per ST-056).
- Hard-delete operations on user-owned data trigger an `audit_logs` write before the delete.

### Required vs optional
- Required fields are non-null at write time. Mongoose `required: true`.
- Optional fields are nullable; default `null` not `undefined` for query consistency.

### Date / time
- All timestamps stored as ISO Date in UTC.
- API serialisation: ISO 8601 strings.

---

## Schema — `organisations`

The Trackwise tenant. Single deployment hosts many.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | Tenant identifier |
| `name` | String | Yes | Display name |
| `logo_url` | String | No | CDN-hosted logo |
| `created_at` | Date | Yes | Mongoose timestamp |
| `updated_at` | Date | Yes | Mongoose timestamp |

**Indexes:** `_id` (default).

**Notes:**
- `github_sync_interval_minutes` — **removed** per ADR-008 amendment. Cache TTL is fixed at 60s globally and not configurable per org.
- One organisation per Cognito `custom:org_id`. New tenants are provisioned by inserting a row here and creating the first Manager user (see Seeding — ADR-012).

---

## Schema — `users`

Application users. Each row is one Cognito user, plus Trackwise-side metadata.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | Trackwise user id |
| `org_id` | ObjectId | Yes | Tenant scope |
| `cognito_sub` | String | Yes | Cognito subject claim — JWT lookup |
| `full_name` | String | Yes | Display name (ST-050) |
| `email` | String | Yes | RFC 5322 validated; lowercase normalised |
| `role` | Enum | Yes | `cto_ceo` \| `pm` — organisational label only (ADR-003: single application Manager role) |
| `status` | Enum | Yes | `active` \| `disabled` — `disabled` blocks login (ST-053) |
| `last_signed_in_at` | Date | No | Updated on each successful login |
| `created_by_user_id` | ObjectId | No | Manager who created the user |
| `created_at` | Date | Yes | Mongoose timestamp |
| `updated_at` | Date | Yes | Mongoose timestamp |

**Indexes:**
- `org_id + email` (unique) — duplicate email prevention within an org
- `cognito_sub` (unique) — JWT lookup
- `org_id + status` (compound) — User Management list filter

**Notes:**
- `cognito_sub` is the bridge between Cognito (auth source of truth) and Trackwise (application data). Cognito does not store `role`, `status` (Trackwise-side), or `last_signed_in_at` — those live here.
- Hard-delete (ST-054) removes the row and calls Cognito `adminDeleteUser` in the same transaction-equivalent boundary (best-effort; if Cognito call fails, the Mongo delete is rolled back via repository pattern).

---

## Schema — `clients`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | Tenant scope |
| `name` | String | Yes | Display name |
| `industry` | String | No | Free-text; future enhancement may enum this |
| `archived_at` | Date | No | Set by ST-056 (archive client) |
| `created_by_user_id` | ObjectId | Yes | |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Indexes:**
- `org_id + _id` (compound) — Dashboard load (per ADR-002)
- `org_id + archived_at` (compound) — Filter active vs archived

**Notes:**
- "Archive" is a soft state — `archived_at` is set, the row is hidden from default views, but the row and all FK references remain intact.
- "Delete client" (ST-057) is a cascade hard delete — removes the client row, all `projects` for that client, and all dependent records (cr_summaries, sprints, milestones, etc.). This is a Mongo-side cascade orchestrated at the service layer.

---

## Schema — `projects`

The central operational entity. Most of the dashboard hangs off this row.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | Tenant scope |
| `client_id` | ObjectId | Yes | FK → `clients._id` |
| `name` | String | Yes | Display name |
| `code` | String | Yes | Short identifier (e.g. `ALPHA-2026`) |
| `delivery_type` | Enum | Yes | `fixed_scope` \| `fixed_budget` (PRD §3) |
| `go_live_date` | Date | No | Target delivery date |
| `github_repo` | String | No | `org/repo` format |
| `github_org` | String | No | GitHub organisation name |
| `github_oauth_token_encrypted` | String | No | AES-encrypted OAuth token (ADR-004) — encryption key in AWS Secrets Manager |
| `sprint_label_pattern` | String | Yes | Default `sprint-N` (PRD §12.1) |
| `archived_at` | Date | No | Set by future archive action |
| `created_by_user_id` | ObjectId | Yes | |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Indexes:**
- `org_id + client_id` (compound) — per-client project list (ADR-002)
- `org_id + _id` (compound) — direct project lookup
- `org_id + archived_at` (compound) — filter active vs archived

**Removed from prior schema (ADR-008 amendment):**
- `cr_tracker_path` — gone
- `last_synced_at` — gone (no scheduled sync)
- `last_sync_status` — gone

**Renamed from prior schema (ADR-008 amendment):**
- `github_sprint_label_prefix` → `sprint_label_pattern`

**Notes:**
- `current_sprint_number` is **not stored**. Per ST-045, current sprint is derived as the highest `N` from `sprint_label_pattern` matches that has open work items in GitHub.
- OAuth token storage uses AWS KMS via Secrets Manager. The token is decrypted only inside the backend process at GitHub call time; never returned to the client.

---

## Schema — `employees`

Non-login users. PM-managed records of the team available for project assignment.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `name` | String | Yes | |
| `role` | String | Yes | Free-text job title (e.g. `Senior Backend Engineer`) — distinct from `users.role` (system role) |
| `current_assignment_project_id` | ObjectId | No | FK → `projects._id`; null if on bench |
| `availability_status` | Enum | Yes | `available` \| `assigned` \| `partially_available` \| `on_leave` |
| `created_by_user_id` | ObjectId | Yes | |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Indexes:**
- `org_id + current_assignment_project_id` (compound) — project team view
- `org_id + availability_status` (compound) — bench / availability filters

**Notes:**
- `partially_available` semantics are the subject of PRD Open Question #3 (still open). Current treatment: PM flag, no hour computation.
- Employees are not Cognito users — no `cognito_sub`. They have no login.

---

## Schema — `domain_knowledge`

One row per (employee, domain) pair. Separate collection to support efficient cross-employee domain searches (per ADR-002 rationale).

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `employee_id` | ObjectId | Yes | FK → `employees._id` |
| `domain` | String | Yes | E.g. `payments`, `compliance`, `aws-iac`. Free-text in v1.0; controlled vocabulary deferred |
| `years_of_experience` | Number | No | Integer ≥ 0 |
| `notes` | String | No | Free-text, max 500 chars |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Indexes:**
- `org_id + employee_id` (compound) — load all domains for an employee
- `org_id + domain` (compound) — directory search "who knows X"

**Notes:**
- Free-text `domain` value will normalise on lowercase + trim at write time.

---

## Schema — `audit_logs`

Append-only log of admin actions. Required by ADR-003 amendment; foundation for future compliance reporting.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `actor_user_id` | ObjectId | Yes | The user who performed the action |
| `action` | String | Yes | Dotted path — e.g. `user.create`, `user.reset_password`, `user.disable`, `user.delete`, `user.role_change`, `client.archive`, `client.delete` |
| `target_type` | String | Yes | Entity type — `user`, `client`, `project`, etc. |
| `target_id` | ObjectId | Yes | Affected entity id |
| `before` | Object | No | Snapshot of relevant fields before the action (null for create) |
| `after` | Object | No | Snapshot of relevant fields after the action (null for delete) |
| `ip_address` | String | No | Captured from request when available |
| `user_agent` | String | No | Captured from request when available |
| `created_at` | Date | Yes | Indexed; primary sort field |

**Indexes:**
- `org_id + target_type + target_id + created_at` (compound) — "what happened to user X?" timeline
- `org_id + actor_user_id + created_at` (compound) — "what did Manager Y do?" timeline
- `org_id + action + created_at` (compound) — type-of-action queries
- `created_at` — TTL not applied; logs retained indefinitely for v1.0

**Notes:**
- Append-only by convention — no NestJS endpoint or service method updates or deletes audit log rows. Database-level prevention deferred (would require Atlas custom role; M0 doesn't support).
- Field-level diffs (`before`/`after`) are deliberately permissive — services choose which fields are relevant. Avoid storing tokens, passwords, encryption keys.
- Audit writes are best-effort within the same service operation; if the log write fails, the action still succeeds and the failure is logged to CloudWatch. This avoids audit infrastructure becoming a single point of failure for user-facing operations.

---

## Relationships summary

```
organisations
   │
   ├── users (org-scoped Cognito-linked accounts)
   │      │
   │      └── audit_logs (actor_user_id, target_user_id)
   │
   ├── clients (org-scoped)
   │      │
   │      └── projects
   │             │
   │             ├── (project-operational entities — see ADR-010)
   │             └── (github cache rows — see ADR-011)
   │
   └── employees (org-scoped, non-login)
          │
          └── domain_knowledge (1:many)
```

All FK references are scoped within `org_id` — no cross-tenant references possible.

---

## Mongoose schema conventions (NestJS)

```typescript
// Example pattern — illustrative, not authoritative implementation
@Schema({ collection: 'users', timestamps: true })
export class User {
  @Prop({ type: SchemaTypes.ObjectId, required: true, index: true })
  org_id: Types.ObjectId;

  @Prop({ type: String, required: true, unique: true })
  cognito_sub: string;

  @Prop({ type: String, required: true })
  full_name: string;

  @Prop({ type: String, required: true, lowercase: true, trim: true })
  email: string;

  @Prop({ type: String, enum: ['cto_ceo', 'pm'], required: true })
  role: 'cto_ceo' | 'pm';

  @Prop({ type: String, enum: ['active', 'disabled'], required: true, default: 'active' })
  status: 'active' | 'disabled';

  @Prop({ type: Date, default: null })
  last_signed_in_at: Date | null;

  @Prop({ type: SchemaTypes.ObjectId, ref: 'User', default: null })
  created_by_user_id: Types.ObjectId | null;
}
```

**Conventions:**
- One schema class per collection, one file per schema, in `src/<module>/schemas/<entity>.schema.ts`.
- `@Schema({ timestamps: true })` always — automatic `created_at` / `updated_at`.
- Compound indexes declared via `<Schema>.index({ org_id: 1, email: 1 }, { unique: true })` after class definition.
- Enum values defined as TypeScript string-literal unions and reused in DTO classes.

---

## Rationale

### Separate `users` collection (not embedded in `organisations`)
- Users have their own lifecycle (created, disabled, deleted) independent of org.
- Audit log references `actor_user_id` and `target_user_id` — needs a stable user identifier.
- Embedding would prevent efficient `cognito_sub` lookups (the JWT-verification hot path).

### `cognito_sub` as the bridge field
- Cognito subject is immutable and unique within a User Pool.
- Email can change; `cognito_sub` cannot.
- Keeping the JWT verification path single-field-indexed (`cognito_sub` unique) is the cheapest hot-path lookup possible.

### `audit_logs` as a separate collection (not append to user record)
- Audit volume grows independently of user count.
- Querying "what happened to user X" needs an index that audit-on-user wouldn't provide.
- Separation also lets us TTL or archive logs differently from user records when scale demands.

### Role enum on `users` despite single Manager system role
- ADR-003 establishes a single application role. `role` here is the **organisational label** ("CTO/CEO" vs "PM") for display and reporting only.
- Storing it costs nothing; not storing it would mean the User Management screen has no way to display role.
- If the system role list ever expands (viewer, employee), this field can be re-purposed.

### Free-text `domain` and `industry`
- v1.0 has fewer than ~50 employees per org; controlled vocabulary is overkill.
- Trim + lowercase normalisation at write avoids the worst search inconsistencies.
- Promotion to enum or reference table deferred until directory search reveals real coupling needs.

### `archived_at` over a `status` enum on `clients` / `projects`
- Boolean state ("active" vs "archived") fits a single nullable timestamp better than a separate enum.
- `archived_at` carries information ("when") that `status: archived` does not.
- Default views filter on `archived_at IS NULL`; archive-aware views remove the filter.

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Embed `users` array inside `organisations` document | Documents grow unbounded; JWT verification needs a global `cognito_sub` index that embedded arrays don't support cleanly |
| Use Cognito as the source of truth for `email` and `full_name` (no Trackwise mirror) | Every screen displaying a user would require a Cognito `adminGetUser` call — expensive and slow. Mirror is the standard pattern |
| Single `audit_logs` collection across all tenants without `org_id` | Violates the ADR-002 tenancy invariant. Cross-tenant audit queries are not a v1.0 requirement |
| Soft-delete on every collection | Adds complexity (every query must filter on `deleted_at IS NULL`); no v1.0 use case beyond the explicit archive lifecycle on `clients` / `projects` |
| Composite primary keys (e.g. `(org_id, email)`) | MongoDB does not support composite primary keys; the equivalent is the unique compound index already specified |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| `audit_logs` retained forever | Acceptable at 5–8 orgs; ~tens of writes per org per day | Atlas storage approaches limit; introduce monthly archival to S3 |
| Audit writes are best-effort | CloudWatch alerts on audit failure spikes | Compliance audit requires guaranteed audit write ordering |
| Free-text `industry` and `domain` | Search inconsistency from typos | Search reveals real coupling — promote to enum or reference list |
| Cognito + Mongo two-step user delete | Service layer handles best-effort consistency | Real two-phase commit needed (e.g. compliance requirement) |

---

## Consequences

- A new NestJS module `users` is required (currently authentication is in an `auth` module per ADR-003; a separate `users` module owns the data model).
- An `audit_logs` module is required, exposed as a service injectable that other modules call after sensitive operations.
- The `JwtAuthGuard` (ADR-003) must enrich the request context with the looked-up `users._id` (not just `cognito_sub`) so audit writes can record `actor_user_id` directly.
- Seed data must include at least one Cognito user + matching `users` row per organisation for app to be functional after deploy. See ADR-012.
- ST-050 (Create user) implementation must call Cognito `adminCreateUser` and `users` insert in the same service method, with rollback on Cognito failure. Same pattern for ST-054 (Delete user).

---

## Review Trigger

Revisit this ADR when:
- Employee self-service login is added (employees become users)
- A read-only viewer role is added (system-role expansion)
- Audit log volume exceeds 100k rows per org
- Free-text fields (`industry`, `domain`) require enum promotion
- Atlas tier is upgraded — re-evaluate index coverage with Performance Advisor
