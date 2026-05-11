# ST-063 — Sprint Health UI — consolidated `Overall` view across sprints

**Story ID:** ST-063
**Epic:** EP-10 — Sprint Health
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM, when I land on Project Detail's Sprint Health section I want a consolidated view across all sprints — so I can see trend at a glance without clicking through each sprint individually.

---

## Acceptance Criteria

### Default state

- [ ] `Overall` is the default dropdown selection when Sprint Health is first loaded
- [ ] Selecting `Overall` triggers `GET /api/v1/projects/{id}/sprint-health` (no `?sprint=` param) and renders the consolidated view

### Consolidated view — minimal v1.0 shape

- [ ] **Summary card** at the top: four big numbers from the `summary` block
  - Total stories: `total_stories_completed` / `total_stories_planned`
  - Total bugs: `total_bugs_resolved` / `total_bugs_logged`
  - Total items carried forward: `total_items_carried_forward`
  - Sprints tracked: count of entries in `sprints` array
- [ ] **Per-sprint trend table** — one row per sprint in `sprints` array, columns:
  - Sprint label (e.g. `sprint-3`)
  - Epics (count)
  - Stories planned / completed
  - Bugs logged / resolved
  - Carryover (combined story + bug + epic moving-forward total)
- [ ] Sprints listed in ascending order; current sprint row visually distinguished (highlight or `(current)` badge)

### States

- [ ] **Loading** — skeleton table while the API call is pending
- [ ] **Project has no sprints** — same empty state as ST-062 ("No sprints found…")
- [ ] **GitHub unavailable with cold cache** — table renders any rows the cache still holds; rows that can't render show "—" cells; banner above the table reads "Some sprint data is currently unavailable — showing what we have"

### Behaviour

- [ ] Clicking a sprint row in the table acts as a shortcut: dropdown switches to that sprint, view re-renders to ST-062's per-sprint layout
- [ ] URL reflects `Overall` state by omitting the `?sprint=` parameter (or `?sprint=overall`)

---

## Notes

- This is intentionally minimal — PM elected to defer the polished consolidated visualisation. Ship trend-table format, revisit after first usage
- Charts (burn-up, velocity trend) are deferred. If PM asks for them post-launch, they get folded into this story's follow-up or a v1.1 story
- No CSV export, no print view for v1.0
- Work-item vocabulary: "epics", "stories", "bugs"

---

## Dependencies

- ST-061 (Sprint Health API) — provides the data
- ST-062 (per-sprint view) — shares the dropdown and section shell; click-through requires ST-062 to be live
