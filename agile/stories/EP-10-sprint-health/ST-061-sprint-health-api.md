# ST-061 — Sprint Health read model and `GET /projects/{id}/sprint-health` API

**Story ID:** ST-061
**Epic:** EP-10 — Sprint Health
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As a frontend developer, I need a single backend endpoint that returns sprint health for either one specific sprint or all sprints consolidated, so the Sprint Health UI can render both the per-sprint and `Overall` views from one call without orchestrating multiple requests.

---

## Acceptance Criteria

### Endpoint contract

- [ ] `GET /api/v1/projects/{id}/sprint-health` is implemented
- [ ] Accepts optional `?sprint=` query parameter:
  - omitted or `?sprint=overall` — consolidated payload (default)
  - `?sprint=N` (e.g. `?sprint=3`) — per-sprint payload for `sprint-3`
- [ ] Endpoint requires authentication and enforces the same project access rules as other project endpoints
- [ ] Response uses snake_case for all fields (per API contract)
- [ ] Endpoint reuses the 60s GitHub cache layer from ADR-011 — no new cache collections

### Per-sprint payload (`?sprint=N`)

- [ ] `sprint_label` — the label string, e.g. `sprint-3`
- [ ] `sprint_window` — `{ start_date, end_date }` derived from label creation / milestone due-date (rule locked during grooming)
- [ ] **Epics & Stories block:**
  - [ ] `epic_count` — distinct milestones with at least one issue carrying the sprint label
  - [ ] `story_count` — issues with `type:story` label carrying the sprint label
  - [ ] `multi_sprint_items` — array of `{ id, title, type, sprint_labels[] }` for any item carrying more than one `sprint-K` label (carryover signal)
- [ ] **Planned vs Completed block:**
  - [ ] `stories` — `{ planned, completed }` counts
  - [ ] `bugs` — `{ planned, completed }` counts
  - [ ] `epics` — `{ planned, completed }` counts; `completed` always 0 in v1.0 (see EP-10 Notes)
- [ ] **Bugs Logged vs Resolved block:**
  - [ ] `bugs_logged_in_sprint` — bugs whose `created_at` falls inside `sprint_window` (regardless of label)
  - [ ] `bugs_pulled_forward` — bugs labelled with this sprint that were created before `sprint_window.start_date`
  - [ ] `bugs_resolved_in_sprint` — bugs labelled with this sprint with status `closed`
- [ ] **Carryover (end-of-sprint) block:**
  - [ ] `stories_moving_forward` — stories carrying this sprint label *and* a later `sprint-K` label (K > N)
  - [ ] `bugs_moving_forward` — same rule for bugs
  - [ ] `epics_moving_forward` — milestones with any story or bug moving forward (transitive)

### Consolidated payload (`?sprint=overall` or omitted)

- [ ] `sprints` — array, one entry per sprint that has ever existed for the project, each containing the same four blocks above (slimmed to counts only — no `multi_sprint_items` array; UI doesn't need per-item list at Overall level)
- [ ] `summary` — totals across all sprints: `total_stories_planned`, `total_stories_completed`, `total_bugs_logged`, `total_bugs_resolved`, `total_items_carried_forward`
- [ ] Sprints listed in ascending order by sprint number (`sprint-1`, `sprint-2`, …)

### Data sources

- [ ] Backend reads sprint label data, milestone data, and issue metadata via the GitHub cache layer (ADR-011)
- [ ] No new database collections required — all data derives from existing cache + Trackwise project metadata
- [ ] If GitHub is unreachable and cache is cold: response includes `data_available: false` with `reason: "github_unavailable"` for the affected blocks; rest of the payload still returned
- [ ] Endpoint covered by integration tests including the GitHub-unavailable case and both `?sprint=` modes

---

## Notes

- Carryover detection uses GitHub's append-only label semantics (PRD §12.1 rule). A story labelled `sprint-2` and `sprint-3` is treated as carryover from sprint-2 into sprint-3
- Epic "completed in sprint" is meaningless because epics span sprints — `completed` count always returns 0 for the epic line in v1.0. PM may revisit
- Sprint window date rule (label creation vs milestone due-date vs first issue close) is the one item to confirm at grooming — multiple defensible options exist; pick one and document
- Performance target: per-sprint payload <500ms p95 with cache warm; consolidated payload <2s p95 with cache warm (scales with sprint count)
- This endpoint is read-only — no mutations, no writes back to GitHub

---

## Dependencies

- ST-045 (sprint label sync via cache) — required for sprint membership
- ST-044 (milestone sync via cache) — required for epic counts
- ADR-011 (GitHub Mirror Cache) — cache layer this endpoint depends on
