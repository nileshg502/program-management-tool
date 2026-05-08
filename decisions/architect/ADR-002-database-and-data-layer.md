# ADR-002 — Database and Data Layer

**Date:** 2026-05-05 (amended 2026-05-08)
**Status:** Approved (with amendments — see "Amendment 2026-05-08")
**Decider:** Architect + PM

---

## Context

Trackwise is a multi-tenant application with 9 data entities defined in the PRD. The database choice (MongoDB Atlas) was decided in ADR-001. This ADR defines how data is structured, how multi-tenancy is enforced at the data layer, and what indexes are required from day one.

---

## Decision

### Multi-Tenancy Model
Single database, single cluster. Every document in every collection carries an `org_id` field. All queries are scoped to `org_id` at the NestJS service layer — no query executes without an `org_id` filter.

### Collections
One collection per PRD entity:

| Collection | Key Fields |
|------------|-----------|
| `organisations` | `_id`, `name`, `logo`, `github_sync_interval_minutes` |
| `clients` | `_id`, `org_id`, `name`, `industry` |
| `projects` | `_id`, `org_id`, `client_id`, `name`, `code`, `delivery_type`, `go_live_date`, `github_repo` |
| `employees` | `_id`, `org_id`, `name`, `role`, `current_assignment_project_id`, `availability_status` |
| `domain_knowledge` | `_id`, `org_id`, `employee_id`, `domain`, `years_of_experience` |
| `milestones` | `_id`, `org_id`, `project_id`, `name`, `due_date`, `percent_complete`, `status` |
| `sprints` | `_id`, `org_id`, `project_id`, `number`, `name`, `stories_committed`, `stories_complete`, `blocked_count`, `github_label` |
| `cr_summaries` | `_id`, `org_id`, `project_id`, `total`, `absorbed_count`, `absorbed_hours`, `converted_count`, `pending_count`, `pending_hours`, `removed_count` |
| `alerts` | `_id`, `org_id`, `project_id`, `type`, `trigger`, `sent_to`, `status`, `created_at` |

### Indexes

| Collection | Index Fields | Type | Reason |
|------------|-------------|------|--------|
| All collections | `org_id` | Single | Tenant scoping on every query |
| `clients` | `org_id + _id` | Compound | Dashboard loads clients per org |
| `projects` | `org_id + client_id` | Compound | Dashboard loads projects per client |
| `projects` | `org_id + _id` | Compound | Project detail view lookup |
| `employees` | `org_id + current_assignment_project_id` | Compound | Availability and bench view |
| `employees` | `org_id + availability_status` | Compound | Filter by availability when scoping new projects |
| `domain_knowledge` | `org_id + employee_id` | Compound | Load domains per employee |
| `domain_knowledge` | `org_id + domain` | Compound | People directory search by domain |
| `milestones` | `org_id + project_id` | Compound | Project detail view — all milestones per project |
| `sprints` | `org_id + project_id` | Compound | Project detail view — current sprint per project |
| `cr_summaries` | `org_id + project_id` | Unique compound | One CR summary per project per org |
| `alerts` | `org_id + project_id + status` | Compound | Unread alert count per project |

### ODM
Mongoose via `@nestjs/mongoose`. Schemas defined with TypeScript decorators. All schema definitions live in a dedicated `schemas/` folder per NestJS module.

---

## Rationale

### Single database with org_id
- Atlas M0 free tier supports one cluster — separate databases per tenant share the same 512MB limit with no isolation benefit at this tier.
- `org_id` filter on every query is the standard pattern for this scale.
- NestJS service layer enforces the filter — no raw queries bypass it.

### org_id as leading index field
- MongoDB uses the leading field of a compound index for initial filtering.
- Placing `org_id` first ensures every query filters by tenant before scanning any further — prevents cross-tenant data leakage in query performance and result sets.

### Separate domain_knowledge collection
- Domain knowledge is a 1:many relationship (one employee, many domains).
- Embedding as an array inside the employee document is simpler but makes domain-based search across employees expensive.
- A separate collection with `employee_id` foreign reference enables efficient `org_id + domain` index queries for the people directory search.

### cr_summaries as a separate collection
- The PRD forensic summary is a read-heavy aggregated view — total CRs, absorbed, converted, pending, removed.
- Storing as a pre-aggregated document per project avoids expensive aggregation pipeline queries on every dashboard load.
- Updated by the sync or forensic process when CR tracker changes.

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| One database per tenant | M0 shares 512MB across all databases on the cluster — no real isolation benefit; connection overhead grows with org count |
| Embed domain_knowledge inside employee document | Makes cross-employee domain search expensive — requires full collection scan or text index workaround |
| Embed milestones/sprints inside project document | Documents grow unbounded as sprints accumulate; individual milestone queries become expensive |
| Compute cr_summaries on the fly | Aggregation pipeline on every dashboard load — unnecessary at this scale when data changes only on sync |

---

## Known Limitations and Scaling Decisions

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| Single `org_id` filter is the only tenant barrier | Enforced strictly at NestJS service layer — every repository method receives `org_id` from the auth context | Any new query added without service-layer enforcement |
| Atlas M0 — no performance monitoring | Indexes defined upfront from known query patterns | Upgrading to M10+ — enable Atlas Performance Advisor |
| Atlas M0 — 500 connection limit | Mongoose connection reuse per Lambda instance — monitored via CloudWatch | Lambda concurrency consistently above 100 |

---

## Consequences

- Every Mongoose schema must include `org_id` as a required, indexed field.
- Every NestJS repository method must accept and apply `org_id` as a mandatory filter — no exceptions.
- A NestJS guard or interceptor must extract `org_id` from the authenticated JWT and inject it into the request context before any service method executes.
- Index creation is handled via Mongoose `autoIndex: true` in development and via Atlas UI or migration script in production.
- `cr_summaries` collection must be updated atomically whenever a CR is logged, decided, or removed.

---

## Review Trigger

Revisit this ADR when:
- Organisation count exceeds 20 — evaluate per-tenant database isolation
- Atlas M0 storage approaches 400MB — upgrade to M10 and enable Performance Advisor
- A new query pattern emerges that does not fit existing indexes

---

## Amendment 2026-05-08 — EP-09, ADR-008, ADR-003 amendment alignment

This ADR established the v1.0 collection list and indexing strategy. Subsequent decisions (PM Sessions 7 + 8) require schema adjustments. This amendment captures them. Detailed schemas for new and amended entities live in [ADR-009](ADR-009-data-model-core-entities.md), [ADR-010](ADR-010-data-model-project-operations.md), and [ADR-011](ADR-011-github-mirror-cache.md). This amendment is the index.

### Schema deletions (from ADR-008 / ADR-007 supersession)

Remove from `projects`:
- `cr_tracker_path` — no longer parsed; CR data is CRUD-owned

Remove from `cr_summaries`:
- `sync_status` — no sync to status
- `last_synced_at` — no sync to time

Remove from `projects` (per ADR-004 amendment):
- `last_synced_at` — replaced by per-cache TTL
- `last_sync_status` — no scheduled sync to track

### Schema renames

On `projects`:
- `github_sprint_label_prefix` → `sprint_label_pattern` — defaults to `sprint-N` (PRD §12.1)

### Schema additions to existing collections

`projects` gains:
- `current_sprint_number` — derived (highest sprint label `N` with open work items); not stored. The PRD originally implied storage; ST-045 derives it. No field needed.
- `archived_at` — nullable timestamp (ST-056 archive)
- `created_by_user_id`, `created_at`, `updated_at` — audit fields
- `org_id` (already present per ADR-002) — unchanged

`cr_summaries` gains (per ST-058):
- `origin` — enum: `deviation` / `client_request` / `internal` / `regulatory`
- `source_deviation_id` — nullable string; **soft reference** for v1.0 (Deviation entity parked); FK validation deferred until ADR-013

`organisations` gains (per ADR-008 — sync interval is no longer relevant):
- `github_sync_interval_minutes` — **removed.** No scheduled sync; cache TTL is fixed at 60s and not configurable per org.

### New collections (registered here, detailed elsewhere)

| Collection | Defined in | Purpose |
|------------|------------|---------|
| `users` | ADR-009 | Application users — Cognito-linked records |
| `audit_logs` | ADR-009 | Admin actions on users (per ADR-003 amendment) and other audited events |
| `cr_records` | ADR-010 | Individual CR records (the per-row data behind `cr_summaries`) — supports ST-058 origin and ST-030 entry |
| `sprint_annotations` | ADR-010 | PM-set sprint health overrides and notes — Trackwise-owned writes layered on top of GitHub-derived sprint state |
| `github_cache_milestones` | ADR-011 | 60s-TTL cache of GitHub milestones per project |
| `github_cache_sprint_membership` | ADR-011 | 60s-TTL cache of GitHub sprint-label issue membership per project |

### Parked (not in v1.0 schema)

| Entity | Owning ADR | Status |
|--------|-----------|--------|
| `qa_reports` | ADR-013 (future) | PM-parked — schema not defined |
| `deviations` | ADR-013 (future) | PM-parked — schema not defined |

### Index additions

| Collection | New Index | Reason |
|------------|-----------|--------|
| `users` | `org_id + email` (unique) | Same-org duplicate email prevention (ST-050) |
| `users` | `cognito_sub` (unique) | Lookup on JWT verification |
| `audit_logs` | `org_id + target_user_id + timestamp` | Audit trail per user |
| `cr_records` | `org_id + project_id + origin` | Dashboard CR-by-origin counts (EP-09) |
| `cr_records` | `org_id + project_id + decision` | CR-by-decision counts |
| `sprint_annotations` | `org_id + project_id + sprint_number` (unique) | One annotation per sprint per project |
| `github_cache_milestones` | `org_id + project_id` (unique) + TTL on `expires_at` | One cache row per project; auto-purge expired |
| `github_cache_sprint_membership` | `org_id + project_id + sprint_label` (unique) + TTL on `expires_at` | One row per project per sprint label; auto-purge expired |

### Consequences (additions)

- The amended `cr_summaries` semantics change: it remains a pre-aggregated read model, but its source of truth is now `cr_records` (CRUD-owned per ADR-008), not `cr-tracker.md`. The aggregate is recomputed on every CR record write.
- `audit_logs` is required for ADR-003 amendment compliance — no admin user action ships without an audit write.
- `github_cache_*` collections require MongoDB TTL indexes (`expires_at` field with `expireAfterSeconds: 0`); MongoDB Atlas supports TTL natively.
- `cr_summaries` may eventually be replaced by an aggregation pipeline once write volume and latency constraints are measured; for v1.0 the pre-aggregated document remains.
