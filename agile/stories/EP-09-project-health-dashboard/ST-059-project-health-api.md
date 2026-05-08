# ST-059 — Project Health read model and `GET /projects/{id}/health` API

**Story ID:** ST-059
**Epic:** EP-09 — Project Health Dashboard
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As a frontend developer, I need a single backend endpoint that returns everything the Project Health Dashboard needs in one call — including a sprint-scoped view selectable via query parameter — so that the UI can render the entire cockpit without orchestrating multiple requests or knowing which data lives in Trackwise versus GitHub.

---

## Acceptance Criteria

### Endpoint contract

- [ ] `GET /api/v1/projects/{id}/health` is implemented and returns a single JSON payload with all sections below
- [ ] Endpoint accepts an optional `?scope=` query parameter:
  - `all_time` — cumulative totals
  - `last_n_sprints:N` (e.g. `last_n_sprints:3`) — average / sum across last N sprints; **default = `last_n_sprints:3`** when no parameter is provided
  - `current` — current sprint only
  - `sprint:N` (e.g. `sprint:5`) — one specific historical or current sprint
- [ ] Scope only re-scopes the **KPI summary block** (status, sprint velocity, sprint progress, open CRs) and the deviation running-balance block. Project metadata, milestone list, and CR section totals are scope-independent
- [ ] Endpoint requires authentication and enforces the same project access rules as the existing project detail endpoints
- [ ] Response payload uses snake_case for all fields (per API contract)

### Payload sections

- [ ] **Project section** — `project_id`, `project_name`, `client_name`, `delivery_type`, `go_live_date`, `overall_status` (Green / Amber / Red, derived from worst sub-status)
- [ ] **KPI summary section** (scope-aware):
  - `status` — On Track / At Risk / Behind, computed for the requested scope per PRD §12.2
  - `sprint_velocity` — points/sprint, single value or average depending on scope
  - `sprint_progress` — `committed_count`, `completed_count`, `carryover_count`, `fresh_count` (where carryover counts stories+bugs that already carried a `sprint-K` label where K < the scope's lowest sprint)
  - `open_crs` — count of CRs raised in the selected scope, plus `pending_hours` summary
- [ ] **Sprint section** — current sprint name, committed count, completed count, blocked count, sprint status; historical totals across all sprints to date (`total_committed`, `total_completed`, `total_blocked`); always all-time, ignores `?scope=`
- [ ] **Milestone section** — list of milestones with `name`, `due_date`, `status` (On Track / At Risk / Behind), `open_count`, `closed_count`
- [ ] **QA section** — `data_available: false` with `reason: "awaiting qa schema"` until the QA Report entity is defined (parked by PM). Once defined: current sprint QA report (pass count, fail count, open defects by severity) and cumulative QA totals
- [ ] **CR section** — totals object: `total_count`, `by_origin` ({ `deviation`, `client_request`, `internal`, `regulatory` }), and **two decision breakdowns** keyed to origin:
  - `deviated_decisions` — { `absorbed`, `converted`, `pending`, `removed` } counts and hours (origin = `deviation`)
  - `client_requested_decisions` — { `approved`, `rejected`, `pending`, `delivered` } counts and hours (origin = `client_request`)
- [ ] **Deviation section** — running-balance shape, scope-aware:
  - For `sprint:N` scope: `opening_balance` (totals as of sprint start), `activity_during_sprint` (new detections, decisions made), `closing_balance` (totals as of sprint close)
  - For `all_time` scope: single object with project-lifetime totals (no opening/closing concept)
  - Until the Deviation entity is defined (parked by PM): `data_available: false` with `reason: "awaiting deviation schema"`

### Data ownership

- [ ] Backend reads CR, deviation, QA, sprint health, and project metadata directly from the Trackwise database
- [ ] Backend reads milestone metadata and sprint membership from GitHub via server-side authenticated calls, cached for 60 seconds per project
- [ ] Carryover detection uses GitHub label history: a story or bug labelled `sprint-N` is treated as carryover if it also carries any `sprint-K` label where K < N. Requires the append-only label rule in PRD §12.1
- [ ] If GitHub is unreachable or returns an error, response includes the cached value if available; if no cache exists, milestone and sprint sections include `data_available: false` and a `reason` string — the rest of the payload is still returned
- [ ] Frontend has no direct path to GitHub — all dashboard data flows through this endpoint
- [ ] Endpoint is covered by integration tests including the GitHub-unavailable degradation case and each `?scope=` value

---

## Notes

- The 60-second TTL cache is server-side only. There is no "last synced" field in the response and no UI affordance for it (per EP-09 scope).
- PRD Open Questions #1 (sprint label `sprint-N`) and #2 (At Risk threshold 10/25 burn-rate) — **resolved 2026-05-08**. This story is unblocked on those points.
- Work-item vocabulary is **epics / stories / bugs** — no "issues." Sprint counts cover stories + bugs; epics are excluded from sprint-level metrics (epics span sprints by design).
- QA and Deviation sections currently stub with `data_available: false` until PM defines the entities (parked, see EP-09 epic).
- Manual refresh, webhook-driven updates, and cross-project rollups are explicitly out of scope.
- ADR-008 defines the data ownership rule this endpoint implements: frontend reads only Trackwise APIs; backend is the only layer permitted to call GitHub.
- Performance target: dashboard payload returned in under 500ms at p95 with cache warm; under 2s with cache cold (GitHub round-trip included).

---

## Dependencies

- ST-058 (CR origin classification) — required for the two decision-breakdown objects
- ST-045 (sprint label sync) — required for sprint scope resolution and carryover detection
- ST-019 (milestone status calculation) — provides the burn-rate inputs
- PM resolution of QA Report and Deviation entity schemas — gates the corresponding payload sections (currently stubbed)
