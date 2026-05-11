# ST-062 — Sprint Health UI — per-sprint view with sprint selector dropdown

**Story ID:** ST-062
**Epic:** EP-10 — Sprint Health
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM, I want to pick any sprint from a dropdown on the Project Detail page and see exactly what was in that sprint — epics, stories, completion, bugs logged vs resolved, and what carried forward — so I can answer "how did sprint-N actually go?" in one click.

---

## Acceptance Criteria

### Placement and selector

- [ ] Sprint Health section added to Project Detail page, below the existing EP-04 / EP-09 sections
- [ ] Sprint selector is a single dropdown at the top of the Sprint Health section
- [ ] Dropdown options: `Overall` (default, top) + every sprint that has ever existed for the project (`sprint-1`, `sprint-2`, …), ascending order
- [ ] Selecting a specific sprint triggers `GET /api/v1/projects/{id}/sprint-health?sprint=N` and renders the per-sprint view (this story); `Overall` is covered by ST-063
- [ ] Currently active sprint is indicated with a `(current)` suffix in the dropdown
- [ ] Sprint window dates (`start_date — end_date`) shown next to the dropdown selection

### Per-sprint view — four blocks

- [ ] **Epics & Stories block** — card showing `epic_count` and `story_count` as large numbers; below them, a small expandable list "X items in multiple sprints" listing `multi_sprint_items` with their labels (collapsed by default)
- [ ] **Planned vs Completed block** — table or paired-bar visual showing planned vs completed counts for stories, bugs, and epics. Epic completed column displays "—" (not "0") with a tooltip "Epic completion in sprint is not tracked in v1.0"
- [ ] **Bugs Logged vs Resolved block** — three numbers: `bugs_logged_in_sprint`, `bugs_pulled_forward`, `bugs_resolved_in_sprint`. Visual treatment: logged + pulled forward on one side, resolved on the other, net surfaced as "X open at sprint close"
- [ ] **Carryover block** — three numbers: `stories_moving_forward`, `bugs_moving_forward`, `epics_moving_forward`. Labeled "Moving to next sprint" with the next sprint name shown if known (`sprint-N+1`)

### States

- [ ] **Loading** — skeleton placeholder for each block while the API call is pending
- [ ] **Empty sprint** — if a sprint has no items, render "No work items found for sprint-N" in place of the four blocks
- [ ] **GitHub unavailable** — if API returns `data_available: false` for any block, render a small warning banner inside that block with the `reason` text; other blocks still render normally
- [ ] **Project has no sprints yet** — dropdown is disabled and the section shows "No sprints found. Add a `sprint-N` label in GitHub to start tracking sprint health."

### Behaviour

- [ ] Selecting a different sprint re-fetches and re-renders without a full page reload
- [ ] Selection is reflected in the URL as `?sprint=N` query parameter so the view is shareable / bookmarkable
- [ ] Browser back / forward navigation respects the sprint selection

---

## Notes

- This story covers the per-sprint shell + four data blocks. The `Overall` consolidated view is ST-063 and shares the same dropdown
- No drill-down into individual issues for v1.0 — `multi_sprint_items` list shows title + label badges only; clicking does nothing (or links to GitHub if trivial)
- Work-item vocabulary on screen: "epics", "stories", "bugs" — never "issues"
- Hours and money tracking remain out of scope per ADR-008 / EP-10 scope

---

## Dependencies

- ST-061 (Sprint Health API) — provides the data
- Existing Project Detail page layout (EP-04 stories landed)
