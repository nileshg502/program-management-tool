# ADR-010 — Data Model: Project Operations

**Date:** 2026-05-08
**Status:** Approved
**Decider:** Architect

---

## Context

[ADR-009](ADR-009-data-model-core-entities.md) covers the core entities. This ADR specifies the **operational entities** — those that change frequently as projects move through sprints and accumulate change requests:

- `cr_records` (new) — individual CR rows
- `cr_summaries` (existing, semantic shift) — pre-aggregated read model
- `sprint_annotations` (new) — PM-set sprint health overrides
- `alerts` (existing, schema clarified)

This ADR adopts the global conventions defined in ADR-009 (snake_case, `org_id` tenancy, Mongoose timestamps, hard-delete default).

GitHub-derived entities (sprint membership, milestone metadata) are **not stored here** — they live in the cache collections defined in [ADR-011](ADR-011-github-mirror-cache.md).

---

## Decision — `cr_records`

The new entity introduced by ST-058. One row per individual CR. Source of truth for CR data; `cr_summaries` is a derived aggregate.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | Tenant scope |
| `project_id` | ObjectId | Yes | FK → `projects._id` |
| `title` | String | Yes | One-line CR description |
| `description` | String | No | Long-form description (max 5000 chars) |
| `origin` | Enum | Yes | `deviation` \| `client_request` \| `internal` \| `regulatory` (ST-058) |
| `source_deviation_id` | String | No | Soft reference; null when `origin ≠ deviation`. FK validation deferred until Deviation entity ships (ADR-013) |
| `decision` | Enum | Yes | See Decision Vocabulary below |
| `estimated_hours` | Number | No | Decimal ≥ 0 |
| `actual_hours` | Number | No | Decimal ≥ 0; populated post-delivery |
| `raised_at` | Date | Yes | When the CR was created |
| `raised_in_sprint` | Number | No | Sprint number active at `raised_at` (computed from `sprint_label_pattern`); null if pre-sprint backlog |
| `decided_at` | Date | No | When `decision` was last set to a non-`pending` value |
| `decided_in_sprint` | Number | No | Sprint number at `decided_at` |
| `decided_by_user_id` | ObjectId | No | FK → `users._id`; the Manager who recorded the decision |
| `delivered_at` | Date | No | Only relevant for `origin=client_request`, `decision=delivered` |
| `delivered_in_sprint` | Number | No | Sprint number at `delivered_at` |
| `requested_by` | String | No | Free-text — for `origin=client_request`, the client stakeholder name |
| `created_by_user_id` | ObjectId | Yes | |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Indexes:**
- `org_id + project_id + origin` (compound) — dashboard CR-by-origin counts (EP-09)
- `org_id + project_id + decision` (compound) — CR-by-decision counts
- `org_id + project_id + raised_in_sprint` (compound) — "CRs raised in sprint N" (Open CRs tile)
- `org_id + project_id + raised_at` (compound) — chronological CR list

### Decision Vocabulary (per origin)

Decisions use a **single enum field** `decision` with values valid across origins. Service-layer validation enforces which values are valid for which origin.

| `origin` | Valid `decision` values |
|----------|------------------------|
| `deviation` | `pending` \| `absorbed` \| `converted` \| `removed` |
| `client_request` | `pending` \| `approved` \| `rejected` \| `delivered` |
| `internal` | `pending` \| `accepted` \| `rejected` \| `completed` |
| `regulatory` | `pending` \| `mandatory` \| `completed` |

The full enum list on the field: `pending`, `absorbed`, `converted`, `removed`, `approved`, `rejected`, `delivered`, `accepted`, `completed`, `mandatory`. Validation by origin is a NestJS service-layer rule, not a Mongoose enum constraint (Mongoose doesn't support cross-field enum gating natively).

### `source_deviation_id` rules

- Required null for `origin ≠ deviation`
- Optional for `origin = deviation` in v1.0 (PM-parked Deviation entity)
- Stored as String for v1.0 — switches to ObjectId once `deviations` collection ships (ADR-013)
- Strict FK validation deferred until ADR-013

### Lifecycle

- CR created with `decision: pending`
- Manager edits the CR to set `decision`; service layer validates and sets `decided_at`, `decided_in_sprint`, `decided_by_user_id`
- For `origin=client_request, decision=delivered`, service layer sets `delivered_at`, `delivered_in_sprint` from `decided_at` (collapsed lifecycle for v1.0; split into two states later if PM raises it)
- Hard delete of CR row triggers `cr_summaries` recomputation in the same service operation

---

## Decision — `cr_summaries` (semantic shift)

Existing collection from ADR-002. **No longer parsed from `cr-tracker.md`** (per ADR-008 supersession of ADR-007). Now a **pre-aggregated read model** computed from `cr_records`.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `project_id` | ObjectId | Yes | Unique with `org_id` (one summary per project per org) |
| `total_count` | Number | Yes | All CRs |
| `by_origin` | Object | Yes | `{ deviation: n, client_request: n, internal: n, regulatory: n }` |
| `deviated_decisions` | Object | Yes | `{ pending: { count, hours }, absorbed: { count, hours }, converted: { count, hours }, removed: { count, hours } }` |
| `client_requested_decisions` | Object | Yes | `{ pending: { count, hours }, approved: { count, hours }, rejected: { count, hours }, delivered: { count, hours } }` |
| `internal_decisions` | Object | Yes | `{ pending, accepted, rejected, completed }` (count, hours each) |
| `regulatory_decisions` | Object | Yes | `{ pending, mandatory, completed }` (count, hours each) |
| `last_recomputed_at` | Date | Yes | Timestamp of last aggregation |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Removed fields** (per ADR-008 amendment):
- `sync_status` — no sync to status
- `last_synced_at` — replaced semantically by `last_recomputed_at`

**Indexes:**
- `org_id + project_id` (unique compound) — one summary per project (per ADR-002)

### Recomputation

- On every `cr_records` create, update, or delete: the affected project's summary is recomputed and upserted in the same service-layer transaction (Mongo session).
- Recomputation is a single aggregation pipeline grouping `cr_records` by origin and decision; deterministic and cheap at v1.0 scale (~hundreds of CRs per project max).
- A "rebuild all summaries" admin job exists for migration / drift-recovery — runs offline, no live trigger.

### Why keep `cr_summaries` as a separate collection at all?

- Dashboard reads the summary in O(1) — single document fetch.
- Aggregating from `cr_records` on every dashboard load would be `N` documents read per project per dashboard call.
- The recomputation cost is paid once per CR write (rare); read cost is amortised across many dashboard loads (frequent). Right tradeoff.

---

## Decision — `sprint_annotations`

PM-set overlays on top of GitHub-derived sprint state. Allows PMs to annotate sprint health with context that GitHub labels cannot express (e.g. "this sprint slipped because of a key absence").

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `project_id` | ObjectId | Yes | FK → `projects._id` |
| `sprint_number` | Number | Yes | Matches `sprint-N` label suffix (ADR-008 / PRD §12.1) |
| `pm_status_override` | Enum | No | `on_track` \| `at_risk` \| `behind` — overrides computed status if set |
| `pm_notes` | String | No | Free-text, max 2000 chars |
| `key_events` | Array<String> | No | Bullet-list of significant sprint events |
| `created_by_user_id` | ObjectId | Yes | |
| `created_at` | Date | Yes | |
| `updated_at` | Date | Yes | |

**Indexes:**
- `org_id + project_id + sprint_number` (unique compound) — one annotation per sprint per project

**Behaviour:**
- If `pm_status_override` is set, the dashboard's Status tile uses it instead of the burn-rate computation (PRD §12.2). UI shows a small "(PM override)" indicator beside the badge.
- Absence of an annotation row for a sprint is normal — most sprints don't need annotation. Dashboard renders without it.
- This collection is the answer to "PM needs to explain a sprint's outcome" — not a replacement for sprint membership data (which lives in GitHub / cache).

---

## Decision — `sprints` (deprecated by this ADR)

ADR-002 originally defined a `sprints` collection with fields like `stories_committed`, `stories_complete`, `blocked_count`, `github_label`. **This collection is removed in v1.0** because:

- Sprint membership and counts come from GitHub via the cache (ADR-011).
- The PM-side sprint context goes into `sprint_annotations` (above).
- A Trackwise-side sprint table would duplicate cache data and create write-side consistency questions.

**Migration:** since no production data exists, the collection is simply not created. Any existing references in code or other ADRs are corrected.

---

## Decision — `alerts`

Existing collection from ADR-002. Schema clarified for EP-07 (Alerts & Notifications).

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `project_id` | ObjectId | Yes | FK → `projects._id` |
| `type` | Enum | Yes | `milestone_at_risk` \| `milestone_behind` \| `sprint_blocked_threshold` \| `cr_pending_overdue` \| `domain_mismatch` |
| `trigger_payload` | Object | No | Snapshot of the data that triggered the alert (e.g. `{ milestone_id, expected_pct, actual_pct }`) |
| `severity` | Enum | Yes | `info` \| `warning` \| `critical` |
| `status` | Enum | Yes | `unread` \| `read` \| `dismissed` |
| `recipients` | Array<ObjectId> | Yes | FK → `users._id` |
| `read_by` | Array<{ user_id: ObjectId, read_at: Date }> | No | Per-user read state |
| `dismissed_at` | Date | No | When all recipients dismissed |
| `created_at` | Date | Yes | |

**Indexes:**
- `org_id + project_id + status` (compound) — unread count per project (per ADR-002)
- `org_id + recipients + status` (multikey compound) — per-user unread filter
- `org_id + type + created_at` (compound) — alert history view per type

**Notes:**
- Alerts are **append-only** by service convention — `dismissed_at` and `read_by` are mutated, but the row is never deleted in v1.0. TTL or archival deferred.
- Alert generation runs as a Lambda triggered by either (a) a CR write that crosses a threshold, or (b) a dashboard read that detects a milestone state change. Generation logic is in EP-07 stories, not this ADR.

---

## Relationships

```
projects
   │
   ├── cr_records  (1:many — individual CRs)
   │      │
   │      └── cr_summaries  (derived aggregate)
   │
   ├── sprint_annotations  (1:many — one per sprint number)
   │
   └── alerts  (1:many — generated by triggers)

(milestones / sprint-membership do NOT live here — see ADR-011)
```

---

## Rationale

### Splitting `cr_records` from `cr_summaries`
- `cr_records` is the system of record (write-heavy, low query rate per row).
- `cr_summaries` is the read model (read-heavy on dashboard, recomputed sparingly).
- This is a classic CQRS split appropriate where read and write patterns diverge sharply.
- An alternative — compute `cr_summaries` on demand from an aggregation pipeline — was rejected because the dashboard hits this view often; the cost would dominate.

### Single `decision` enum across origins (with service-layer validation)
- Storing decisions as a single field keeps query indexes simple (`org_id + project_id + decision` covers all origins).
- Per-origin enums would mean a `decision_<origin>` field per origin — index proliferation, schema change every time a value is added.
- Service-layer validation is the right place for cross-field rules; Mongoose's declarative validators don't express them well.

### `sprint_annotations` instead of a Trackwise `sprints` collection
- The team uses GitHub labels for sprint membership; that's the canonical source.
- Trackwise's value-add is the PM annotation layer — explanations, overrides, context.
- Storing membership counts here would duplicate the cache and require sync — exactly what ADR-008 rejected.

### Soft reference for `source_deviation_id` (string, not ObjectId, no FK constraint)
- The Deviation entity is parked. Using ObjectId now would prematurely commit to a schema; using nothing loses the linkage.
- String type accepts any future identifier scheme (ObjectId hex, custom code, UUID).
- Promotion to ObjectId + FK constraint is a one-shot migration once ADR-013 lands.

### `alerts.recipients` as an array (not a junction table)
- Per-user read state is small (one row per user per alert).
- Multikey index on `recipients` enables efficient per-user query.
- Junction table (`alert_recipients`) would be over-normalised at this scale.

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Separate `decision_history` collection per CR | Audit log already covers high-value lineage (decided_by, decided_at). Full state-change history not needed for v1.0 |
| Compute `cr_summaries` on every dashboard read (no pre-aggregation) | Dashboard read is the hot path; CR write is rare. Inverting the cost split is wrong |
| Keep `sprints` collection as in ADR-002 | Duplicates GitHub cache data; creates sync problem ADR-008 explicitly avoided |
| Per-origin sub-collections (`deviated_crs`, `client_request_crs`) | Same data, more places. Cross-origin queries (total CR count) become joins. Not worth it |
| Embedded annotations array on `projects` document | Documents grow unbounded as sprints accumulate; per-sprint queries become inefficient |
| Strict ObjectId FK validation on `source_deviation_id` from day one | Forces Deviation entity definition before EP-09 can ship. ST-058 is explicitly soft-reference for this reason |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| `cr_summaries` recomputation on every CR write | Acceptable at expected v1.0 scale (~hundreds of CRs per project) | Recomputation latency exceeds 100ms |
| Single-process recompute (no queue) | Acceptable; Mongo session ensures consistency | Write contention emerges; introduce queued recompute |
| `sprint_annotations` not auto-created | PM creates rows on demand; absence is normal | UX wants pre-populated rows for every sprint |
| Soft `source_deviation_id` reference | Service-layer note "deviation entity pending" until ADR-013 | Deviation entity ships |
| Alerts retained forever | Acceptable at v1.0 volume | Storage approaches 50% of Atlas tier |

---

## Consequences

- A new NestJS `crs` module is required, owning both `cr_records` and `cr_summaries` schemas; recomputation logic is internal to this module.
- A new `sprint_annotations` module is required.
- The existing `alerts` module (per EP-07) needs the schema fields above — `severity` and `read_by` are new.
- The dashboard Health API (ST-059) reads `cr_summaries` directly for the CR section (single-document read) and queries `cr_records` for "raised in sprint N" counts on the Open CRs tile.
- The Deviation tab on the dashboard (ST-060) reads `cr_records` filtered by `origin = deviation`; running-balance calculation is a service-layer concern joining `raised_in_sprint` and `decided_in_sprint`.
- Frontend never queries `cr_records` directly — Health API is the only entry point (per ADR-008).

---

## Review Trigger

Revisit this ADR when:
- Deviation entity ships (ADR-013) — `source_deviation_id` upgrades to ObjectId + FK
- QA Reports entity ships (ADR-013) — review whether QA records belong here or get their own ADR
- CR write volume exceeds 1000 CRs per project — reconsider summary recomputation cost
- Sprint membership data needs to be queryable beyond the cache TTL — reconsider `sprints` collection re-introduction
- Alert volume per org exceeds 10k rows — introduce TTL or archival
