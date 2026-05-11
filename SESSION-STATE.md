# Session State

**Last Updated:** 2026-05-11 (Session 9 — PM)
**Current Branch:** master
**Total Delivered:** 0 points across 0 sprints

---

## Current State

Session 9 (PM): new epic **EP-10 — Sprint Health** added with three stories (ST-061 API, ST-062 per-sprint UI, ST-063 consolidated Overall UI). Per-project sprint health view with sprint dropdown, four data blocks per sprint (Epics & Stories, Planned vs Completed, Bugs Logged vs Resolved, End-of-sprint Carryover). Sits underneath EP-09's Sprint Progress tile, not a replacement. Count-based only for v1.0; reuses ADR-011 cache layer with no new DB collections.

Backlog now: **273 points / 63 stories / 10 epics.**

---

## Changes Made This Session (Session 9 — PM)

**1. New epic — EP-10 Sprint Health — DONE**
- File: `agile/epics/EP-10-sprint-health.md`
- Goal: per-project sprint health view answering "what's in this sprint?", "what got done?", "what's leaking forward?"
- Sprint dropdown: `Overall` (default, consolidated) + every sprint label
- Four blocks per sprint: Epics & Stories, Planned vs Completed, Bugs Logged vs Resolved, End-of-sprint Carryover
- Sits underneath EP-09's Sprint Progress tile — not a replacement
- Total: 18 points / 3 stories / Priority P1

**2. New stories — DONE (3 stories, 18 points)**

EP-10 — Sprint Health:
- ST-061 — Sprint Health read model and `GET /projects/{id}/sprint-health` API (P1, 8)
- ST-062 — Sprint Health UI — per-sprint view with sprint selector dropdown (P1, 5)
- ST-063 — Sprint Health UI — consolidated `Overall` view across sprints (P1, 5)

**3. PM defaults documented as assumptions in EP-10 epic**
- Epic = GitHub milestone; "in sprint" = label present
- "Completed" = GitHub `closed` + sprint label present
- "Bug logged in sprint" = `created_at` inside sprint window
- "Pulled forward" = labelled this sprint, created before sprint window
- "Resolved" = GitHub `closed` (QA-verified deferred)
- Carryover for EP-10 includes epics (EP-09's Sprint Progress tile excludes them)
- Sprint window date rule deferred to ST-061 grooming

**4. Backlog updated — DONE**
- Totals: 255 → 273 points, 60 → 63 stories, 9 → 10 epics
- ST-061 / ST-062 / ST-063 rows added under P1 section
- EP-10 row added to Epics Summary
- Grooming note added introducing EP-10 with scope and v1.0 limitations

---

## Decisions Made This Session (Session 9)

**EP-10 scope — APPROVED by PM**
- Per-project only (no cross-project rollup)
- Count-based metrics only for v1.0 — hours / charts / drill-downs deferred
- Sprint dropdown options: `Overall` + every sprint that has ever existed, no cap
- Consolidated `Overall` view ships as a minimal trend table; PM revisits format after first usage
- Carryover for end-of-sprint block includes epics (EP-10 differs from EP-09's Sprint Progress tile which excludes epics)
- Epic "completed in sprint" treated as meaningless in v1.0 — column reads "—" with tooltip, completed count returns 0

---

## Changes Made Previous Session (Session 8 — PM)

**1. New epic — EP-09 Project Health Dashboard — DONE**
- File: `agile/epics/EP-09-project-health-dashboard.md`
- Goal: single-view cockpit per project covering sprint, milestone, QA, CR-by-origin, and deviation data
- Total: 19 points / 3 stories / Priority P1

**2. New stories — DONE (3 stories, 19 points)**

EP-09 — Project Health Dashboard:
- ST-058 — CR Summary — add origin classification (`deviation` / `client_request` / `internal` / `regulatory`) and source deviation link (P1, 3)
- ST-059 — Project Health read model and `GET /projects/{id}/health` API (P1, 8) — gated on PRD Open Questions #1 and #2
- ST-060 — Project Health UI — single-page cockpit screen (P1, 8)

**3. New ADR drafted — ADR-008 Trackwise Data Ownership and Sync Model**
- File: `decisions/architect/ADR-008-data-ownership-and-sync.md`
- Status: Draft (PM-initiated, awaiting Architect review)
- Codifies: three data ownership categories (Trackwise-owned CRUD, GitHub-mirrored read-only, External not-stored); frontend reads only Trackwise APIs; backend uses 60s server-side cache for the two GitHub-mirrored entities; no scheduled sync, no mirror table, no "last synced" UX, no manual refresh button for v1.0
- Supersedes ADR-007's `cr-tracker.md` parsing path

**4. Backlog and epic files updated — DONE**
- Product backlog totals refreshed (236 → 255 points, 57 → 60 stories, 8 → 9 epics)
- ST-058 / ST-059 / ST-060 rows added under P1 section
- Two grooming notes added: one introducing EP-09 with its gating dependencies, one recording the data ownership rule

---

## Decisions Made Previous Session (Session 8)

**Data ownership rule — APPROVED by PM**
- All data displayed in Trackwise UI must be stored in the Trackwise database
- Frontend reads only Trackwise APIs — no direct GitHub calls from the browser
- Backend may call GitHub server-side, but only for milestone metadata and sprint membership
- Server-side cache TTL = 60 seconds; no scheduled sync, no mirror table, no webhooks for v1.0
- No "last synced HH:MM" UI element; no manual refresh button — both deferred
- ADR-008 drafted to codify; ADR-007 to be marked Superseded by Architect

**Project Health Dashboard scope — APPROVED by PM**
- Single page per project, single API call, section per domain (sprint, milestone, QA, CR-by-origin, deviation)
- CR origin classification fixed at four values for v1.0: deviation, client_request, internal, regulatory
- Cross-project rollup, mobile layout, drill-downs, and additional sections (budget, utilisation, blockers) explicitly out of scope for v1.0

**PRD Open Question #1 resolved — APPROVED by PM**
- Sprint label pattern: `sprint-N` (numeric suffix). `sprint_label_pattern` on Project defaults to `sprint-N` per ST-045
- Current sprint is derived (highest `N` with open issues); no `current_sprint_number` field needed
- Multi-sprint issues count toward both sprints; pre-sprint backlog excluded from sprint sections in v1.0
- PRD §12.1 captures the full resolution

**PRD Open Question #2 resolved — APPROVED by PM**
- At Risk / Behind status computed at read time on every dashboard request — no stored status field
- Burn-rate bands (issue-count basis for v1.0): On Track ≥ −10%, At Risk −10% to −25%, Behind < −25% or past due date with open issues
- 10% / 25% bands are starting values; PM will revisit after first usage
- PRD §12.2 captures the full computation, edge cases, and tuning note
- Unblocks ST-019, ST-020, ST-045, ST-059

**EP-09 Deviation and QA schemas parked — DECIDED by PM**
- QA Report and Deviation entities have no backlog stories yet. PM has not finalised the structure.
- Two paths to resolve: PM defines structure → stories created; OR PM provides existing DB → build against that
- Until resolved, ST-059 stubs QA and Deviation sections (`data_available: false`, reason `"awaiting schema"`)
- ST-058's `source_deviation_id` softened to a nullable optional reference for v1.0; strict FK validation deferred
- EP-09 epic file carries a "Blocked-by — structure definition" section flagging the dependency

**EP-09 dashboard design — LOCKED items (PM)**
- **Hours only**, no money tracking in v1.0 — no rate, revenue, or billed/unbilled fields
- **CR section split into two tabs**: Deviated CRs (decisions: absorbed/converted/pending/removed) and Client-Requested CRs (decisions: approved/rejected/pending/delivered). Decision vocabulary differs per origin
- **Sprint scope dropdown** placed near the four KPI tiles. Re-scopes only those four tiles, not the rest of the page. Options: All-time, Last 3 sprints (default), Last 6 sprints, Current sprint, each individual past sprint
- **Carryover line** on Sprint Progress tile counts stories + bugs that carry any prior `sprint-K` label
- **Carryover mechanism** — GitHub labels are append-only; multi-label is the carryover signal. PRD §12.1 needs to capture this rule (PM follow-up)
- **Deviation section** uses three-column running-balance layout when a single sprint is selected (opening / activity / closing); collapses to single-column for All-time
- **Open CRs tile** uses "raised in selected scope" semantic — no audit log / historical state needed for v1.0
- **Work-item vocabulary** = epics / stories / bugs — no "issues." Sprint metrics cover stories + bugs only; epics excluded from sprint tile

## Architect Half (Session 8)

**ADR housekeeping — DONE**
- ADR-008 → Status `Approved` with Architect sign-off (cache strategy, TTL, ownership boundary validated; implementation in ADR-011)
- ADR-007 → Status `Superseded by ADR-008` with explicit warning banner; retained as historical record
- ADR-003 → Amendment 2026-05-08 added: in-app user management actions (reset password, change role, disable, delete) with audit log writes
- ADR-004 → Amendment 2026-05-08 added: scheduled-sync model narrowed by ADR-008; cache mechanism delegated to ADR-011; OAuth token storage retained, sync-tracking fields removed

**ADR-002 amended — DONE**
- Schema deletions: `cr_tracker_path`, `sync_status`, `last_synced_at`, `last_sync_status`, `github_sync_interval_minutes`
- Schema renames: `github_sprint_label_prefix` → `sprint_label_pattern`
- Six new collections registered: `users`, `audit_logs`, `cr_records`, `sprint_annotations`, `github_cache_milestones`, `github_cache_sprint_membership`
- Detailed schemas live in ADR-009 / ADR-010 / ADR-011

**ADR-009 drafted — Data Model: Core Entities**
- Detailed schema for `organisations`, `users`, `clients`, `projects`, `employees`, `domain_knowledge`, `audit_logs`
- Global conventions section (snake_case, ObjectId for FKs, Mongoose timestamps, hard-delete default, audit fields)
- Field-by-field tables, indexes, rationale, alternatives, known limitations, consequences

**ADR-010 drafted — Data Model: Project Operations**
- Detailed schema for `cr_records` (new), `cr_summaries` (semantic shift to derived aggregate), `sprint_annotations` (new), `alerts` (clarified)
- Decision-vocabulary-by-origin table for `cr_records.decision`
- Old `sprints` collection from ADR-002 explicitly deprecated (data lives in cache, annotations layer on top)
- CQRS rationale for `cr_records` ↔ `cr_summaries` split

**ADR-011 drafted — GitHub Mirror Cache**
- Two cache collections (`github_cache_milestones`, `github_cache_sprint_membership`)
- 60s TTL via Mongo TTL index on `expires_at`
- Read-path / cache-miss / failure-mode flows fully specified (ok / partial / stale_serving statuses)
- ETag conditional revalidation, rate-limit headroom analysis, cold-cache handling
- ST-045 redefined: no longer creates `sprints` collection; implements cache fetch + parse logic (BA follow-up to update story text)

**ADR-012 drafted — Seeding Strategy**
- Three-tier model defined; **Tier 1 only implemented in v1.0** per PM Session 8 decision
- Tier 1 (bootstrap): one organisation + one Manager user + TTL/unique indexes + audit log entry
- Mechanism: `nestjs-command` CLI with idempotent upserts; production gating via `TRACKWISE_PRODUCTION_SEED=true` env var
- Cognito user provisioned by deployment pipeline; seed receives `cognito_sub` via env
- Tier 2 (demo) and Tier 3 (migration) documented as sketches; not implemented

**Data dictionary rewritten — DONE**
- File: `architecture/data-model.md` (rewritten in place from v1.0 stub)
- Now canonical reference: 13 collections, full field tables, indexes, rationale links per collection
- Mermaid ER diagram covering all relationships
- ADR map at top; tenant isolation contract; field-type conventions; parked entities section
- Rule established: schema PR must update this dictionary in the same change

---

## Outstanding Architect items (next Architect session)

**For ADR-013 (when PM unblocks)**
- QA Reports entity — schema and entry path
- Deviation entity — schema, lifecycle, FK constraint to upgrade `cr_records.source_deviation_id` from soft string to ObjectId
- BA action: refresh ST-045 text to reflect ADR-011 (no sprints collection; cache fetch + parse)

**Operational follow-ups**
- DevOps to document production seed env vars and Cognito user provisioning in deployment runbook
- Engineering: when implementing `cr_records`, `cr_summaries` recompute is per-write; verify latency at expected scale
- Engineering: `users` module owns user data; `auth` module handles JWT (per ADR-009 consequences)

---

**EP-09 dashboard design — PARKED items (PM elected to resolve internally)**
- CR sub-tab placement (sub-tabs inside Forensic / CRs vs promote to top-level navigation)
- Conversion rate metric (count-based / hour-based / both)
- Internal + regulatory CR treatment (hidden / small footer / third tab)
- Work-item type label convention (`type:story` vs bare labels vs other)
- Epic display in sprint metrics (confirm exclusion or change)
- These do not block ST-059 / ST-060 from sprint entry but should be resolved before implementation
- All items captured in EP-09 epic under "Design — locked vs parked" section so context isn't lost

---

## Changes Made Previous Session (Session 7 — BA)

**1. New stories — DONE (8 stories, 27 points)**

EP-01 — Foundation & Auth (was 23, now 39):
- ST-050 — Create user flow — direct Cognito creation by Manager (P0, 5)
- ST-051 — Manage User — reset password (P2, 3)
- ST-052 — Manage User — change role (P2, 2)
- ST-053 — Manage User — disable / re-enable (P2, 3)
- ST-054 — Manage User — delete user, hard delete (P2, 3)

EP-03 — Client & Project Dashboard (was 22, now 33):
- ST-055 — Manage Client — edit client details (P1, 3)
- ST-056 — Manage Client — archive client (P1, 3)
- ST-057 — Manage Client — delete client, cascade hard delete (P2, 5)

**2. Stories drafted then deleted same session**
- ST-058, ST-059, ST-060, ST-061 (File Preview modal + parsed views) — drafted, then deleted after PM rejected GitHub file reading.

**3. Backlog and epic files updated — DONE**
- EP-01, EP-03 epic story tables and totals updated
- EP-08 epic — kept unchanged at 42 points / 8 stories
- Product backlog — totals, story rows, grooming notes refreshed

---

## Decisions Made Previous Session (Session 7)

**ADR-003 reopen — APPROVED by PM**
- Reset-password, disable/re-enable, and delete user actions move from "Cognito console only" to in-app for v1.0
- Architect to amend ADR-003 in next session — documentation follow-up, not a gate
- ST-051, ST-053, ST-054 are unblocked and sprint-ready

**GitHub file reading — REJECTED by PM**
- Trackwise reads only milestones, sprint labels, and blocked labels from GitHub
- All forensic / compliance / security data lives in Trackwise database
- Frontend reads via Trackwise APIs, not via GitHub Contents API
- ADR-004 stays as-is — no reopen
- ADR-007 needs update — current text describes parsing `cr-tracker.md` from repo; new direction is database-native CR data
- ST-029 (CR Summary entity) + ST-030 (manual CR entry) become *the* path for CR data
- Mockup's multi-file tracker + File Preview modal must be removed in the next UX pass

---

## Session History

| Session | Role | Summary |
|---------|------|---------|
| 1 | PM | Project setup, roles defined, initial structure created |
| 2 | PM | Tool integration and multi-project workflow discussed (deferred) |
| 3 | PM + Forensic | Forensic Role redesigned — 5 documents, CR tracker, templates created. Trackwise PRD v1.0 written |
| 4 | BA | 8 Epics and 49 Stories created. Product backlog updated — 209 points total |
| 5 | Architect | 7 ADRs completed and approved. Architecture documents fully updated |
| 6 | UX Designer | Full mockup rebuild — all 14 screens with design tokens, SVG icons, fixed shell, polished components. Added Manage User drawer (S-15), Manage Client drawer (S-16), File Preview modal (parsed + raw markdown), multi-file tracker, palette comparison tool. Final treatment: Violet → Pink + Warm Cream |
| 7 | BA | 8 new stories (ST-050 → ST-057) covering user creation, manage-user, manage-client. ADR-003 reopen approved by PM. GitHub file reading rejected — ADR-007 needs update, mockup needs File Preview / multi-file removed |
| 8 | PM + Architect | **PM half:** new epic EP-09 + 3 stories (ST-058 → ST-060), ADR-008 drafted, PRD Q1 + Q2 resolved (`sprint-N`, burn-rate 10/25), dashboard design (hours only, two CR tabs, sprint scope dropdown, deviation running-balance, carryover line), QA + Deviation schemas parked. **Architect half:** four new ADRs (ADR-009 Core Entities, ADR-010 Project Operations, ADR-011 GitHub Mirror Cache, ADR-012 Seeding Strategy). ADR-002 amended; ADR-008 Approved; ADR-007 Superseded; ADR-003 + ADR-004 amended. Canonical data dictionary rewritten with Mermaid ER diagram |
| 9 | PM | New epic EP-10 Sprint Health + 3 stories (ST-061 API, ST-062 per-sprint UI, ST-063 consolidated Overall UI). Count-based metrics only for v1.0; reuses ADR-011 cache; sits under EP-09's Sprint Progress tile. PM defaults documented as assumptions inside the epic — sprint window date rule deferred to grooming |

---

## Pending / Next Steps

**Architect actions (next Architect session)**
- **Review and approve ADR-008** (Data Ownership and Sync Model) — drafted by PM in this session. Architect to validate cache strategy, confirm 60s TTL is workable, sign off as Approved
- **Mark ADR-007 as Superseded by ADR-008** — its `cr-tracker.md` parsing path is no longer the implementation path. Document the supersession in ADR-007's Status field and add a pointer to ADR-008
- **Amend ADR-003** — document that reset-password, disable/re-enable, delete user are in-app actions for v1.0 (was: Cognito console only) — carried over from Session 7
- **Verify ADR-004** — confirm no change needed (file reading rejected; original ADR holds) — carried over from Session 7
- **Schema cleanup arising from ADR-008** — remove `cr_tracker_path` from `projects` collection, remove `sync_status` and `last_synced_at` from `cr_summaries`

**UX actions (next UX session)**
- Remove File Preview modal from `design/mockups/trackwise-mockup.html`
- Remove multi-file tracker repeater from Project Settings → GitHub Integration; revert to no file path field at all (no `cr-tracker.md` reference anywhere)
- Remove tracked-files table and related visualisations from Project Detail → GitHub tab; replace with API-driven views of CR Summary / compliance / security data sourced from Trackwise database
- Update Manage Client drawer "Recent activity" mock data so it stops referencing CR-010 parsed from a file

**PRD open questions**
- ~~GitHub label convention for sprints~~ — **resolved 2026-05-08** (sprint-N, see PRD §12.1)
- ~~At Risk vs Behind threshold~~ — **resolved 2026-05-08** (burn-rate 10/25, see PRD §12.2)
- Partially Available definition (hours vs PM flag) → still open, blocks ST-026
- **PRD revision still needed** — add Section for EP-09 Project Health Dashboard scope, CR origin classification, data ownership rule (ADR-008), and EP-10 Sprint Health scope. v1.1 has resolved Q1 + Q2 only

**EP-10 grooming follow-up**
- Sprint window date rule — pick one of: label creation timestamp, milestone due-date, first issue close in sprint. Lock at ST-061 grooming

**Parked by PM (awaiting structure definition)**
- QA Report entity — schema and entry path undefined. Either PM defines, or PM provides existing DB
- Deviation entity — same status. ST-058 references `source_deviation_id` as a soft pointer until resolved
- Both block the QA and Deviation sections of ST-059's payload

**Backlog grooming follow-ups**
- ST-050 supersedes ST-003's "registration" path — when ST-050 is scheduled, re-scope ST-003 to "login + logout only"
- ST-055 makes ST-011's "deactivate" action redundant — re-scope ST-011 when ST-055 is scheduled

**Pending reviews**
- CTO review of forensic templates in `projects/templates/forensic/`
- PM/CTO walkthrough of the rebuilt mockup (post-rebuild, palette finalised, post UX revert)

**Deferred from earlier sessions**
- Tool integration discussion — deferred from Session 2
- Multi-project workflow design — deferred from Session 2
- Future enhancements (PM to specify when ready):
  - Employee self-service login and domain knowledge editing — deferred from ADR-003
  - Email notifications — deferred from ADR-006
  - Monorepo GitHub support — deferred from ADR-004
