# ST-061 — Sprint Health file ingestion and `GET /projects/{id}/sprint-health` API

**Story ID:** ST-061
**Epic:** EP-10 — Sprint Health
**Priority:** P1
**Points:** 8
**Status:** Not Started
**Last Updated:** 2026-05-12 (Session 10 — PM, rescoped)

---

## User Story

As a frontend developer, I need a single backend endpoint that returns sprint health for either one specific sprint or all sprints consolidated, so the Sprint Health UI can render both the per-sprint and `Overall` views from one call. The data is sourced from the project's `sprint-health.md` file (the single source of truth), projected into a DB collection, and served via this endpoint.

---

## Background — file-driven model (Session 10)

Sprint Health is NOT derived from GitHub. The Session 8 ADR-008 / ADR-011 cache path is superseded for Sprint Health specifically.

**Pipeline:**
```
projects/<id>/sprint-health.md  (markdown file, source of truth, updated via Claude chat)
        │
        │ parsed + upserted on a schedule (TBD by Architect)
        ▼
Sprint Health DB collection (mirror of the four tables in the file)
        │
        │ read by API
        ▼
GET /api/v1/projects/{id}/sprint-health
```

The file contains four markdown tables: Sprints, Epics, Stories, Bugs. Each row in the file = one DB row. Schema is documented in `EP-10-sprint-health.md` and in the file itself.

---

## Acceptance Criteria

### File parser + ingestion

- [ ] Parser reads `projects/<project-id>/sprint-health.md` from the program-management-tool repo
- [ ] Parser extracts the four markdown tables (Sprints / Epics / Stories / Bugs) by section heading
- [ ] Each table row is upserted into the corresponding DB collection (keyed by `sprint_id` / `epic_id+sprint_id` / `story_id` / `bug_id`)
- [ ] Rows that exist in DB but not in the file are marked deleted (or removed — Architect decision)
- [ ] `—` is interpreted as null/empty for all fields
- [ ] Malformed table (missing column, bad date, unknown enum value) → ingestion job fails for the affected entity, logs error, leaves DB in its prior state
- [ ] Ingestion schedule: configurable; default 60s poll (matches existing ADR-011 cache cadence for consistency, but the cache itself is not used for Sprint Health)
- [ ] One-shot ingestion endpoint or CLI for manual re-sync

### DB collection schema

- [ ] Four collections (or one collection with a `type` discriminator — Architect decides): `sprint_health_sprints`, `sprint_health_epics`, `sprint_health_stories`, `sprint_health_bugs`
- [ ] Field set matches the file's table columns exactly (see `EP-10-sprint-health.md` field definitions)
- [ ] Each row carries `project_id` for tenant isolation
- [ ] Indexes: `(project_id, sprint_id)` on all four collections

### Endpoint contract

- [ ] `GET /api/v1/projects/{id}/sprint-health` is implemented
- [ ] Accepts optional `?sprint=` query parameter:
  - omitted or `?sprint=overall` — consolidated payload (default)
  - `?sprint=N` (e.g. `?sprint=3`) — per-sprint payload for `sprint-3`
- [ ] Endpoint requires authentication and enforces the same project access rules as other project endpoints
- [ ] Response uses snake_case for all fields (per API contract)

### Per-sprint payload (`?sprint=N`)

- [ ] `sprint_label` — e.g. `sprint-3`
- [ ] `sprint_window` — `{ start_date, end_date }` from the Sprints table
- [ ] **Epics & Stories block:**
  - [ ] `epic_count` — distinct `epic_id` in Epics table where `sprint_id` matches
  - [ ] `story_count` — count of Stories rows where `sprint_id` matches
  - [ ] `carryover_items` — array of `{ id, title, type, carried_from_sprint, carried_to_sprint }` for stories/bugs/epics with either carryover field set
- [ ] **Planned vs Completed block:**
  - [ ] `stories` — `{ planned: count of all stories in sprint, completed: count with status=done }`
  - [ ] `bugs` — `{ planned: count, completed: count with status=resolved or closed }`
  - [ ] `epics` — `{ planned: count, completed: count with status=completed }` (note: epic completion in sprint v1.0 generally returns 0 — see EP-10 Notes)
- [ ] **Bugs Logged vs Resolved block:**
  - [ ] `bugs_logged_in_sprint` — bugs where `sprint_id` matches AND `carried_from_sprint` is null
  - [ ] `bugs_pulled_forward` — bugs where `sprint_id` matches AND `carried_from_sprint` is non-null
  - [ ] `bugs_resolved_in_sprint` — bugs where `sprint_id` matches AND status in (`resolved`, `closed`)
- [ ] **Carryover (end-of-sprint) block:**
  - [ ] `stories_moving_forward` — stories where `sprint_id` matches AND `carried_to_sprint` is non-null
  - [ ] `bugs_moving_forward` — same rule for bugs
  - [ ] `epics_moving_forward` — epics where `sprint_id` matches AND `carried_to_sprint` is non-null (if epics use that field) OR derived from stories/bugs moving forward (Architect decision)

### Consolidated payload (`?sprint=overall` or omitted)

- [ ] `sprints` — array, one entry per sprint in the Sprints table, each containing the same four blocks above (slimmed to counts only — no `carryover_items` array)
- [ ] `summary` — totals across all sprints: `total_stories_planned`, `total_stories_completed`, `total_bugs_logged`, `total_bugs_resolved`, `total_items_carried_forward`
- [ ] Sprints listed in ascending order by sprint number

### Error handling

- [ ] If file is missing for the project → endpoint returns 200 with empty `sprints` array and `data_available: false` flag, reason `"sprint_health_file_not_found"`
- [ ] If most recent ingestion failed → endpoint serves last successful state and surfaces a `last_ingestion_status` field
- [ ] Endpoint covered by integration tests for: file missing, malformed file, both `?sprint=` modes, empty sprint

---

## Notes

- The file is the only write path. The endpoint is read-only. No mutations from the UI side.
- Performance target: per-sprint payload <300ms p95 (no GitHub calls — pure DB read); consolidated <800ms p95
- Architect to decide: one DB collection with type discriminator vs four collections. Recommendation in epic notes is four collections (cleaner separation)
- File path convention: `projects/<project-id>/sprint-health.md`. Project ID matches the slug in the Trackwise `projects` collection

---

## Dependencies

- **No longer depends on** ST-044, ST-045, or ADR-011 (those served the superseded GitHub-cache model)
- New dependency: Sprint Health DB collection schema (Architect — next session)
- New dependency: file parser library (markdown table parsing — picked at grooming)

---

## Out of Scope (deferred to other stories / v1.1)

- File-write API (anything that modifies `sprint-health.md` from inside Trackwise) — file is owned by the chat workflow, not the app
- Git commit / push handling — Claude commits when editing; Trackwise just reads
- Notifications on file change — out of scope
- Audit log of file changes — git log is the audit log
