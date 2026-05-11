# EP-10 — Sprint Health

**Epic ID:** EP-10
**Title:** Sprint Health (per-sprint + consolidated view)
**PRD Reference:** Section 12 (extension — to be added in next PRD revision)
**Priority:** P1
**Status:** Not Started

---

## Goal

Give PM a per-project view of how each sprint actually went — committed vs delivered, bugs logged vs resolved, and what carried forward — plus a consolidated view across all sprints. Answers the questions: "What's in this sprint?", "What got done?", "What's leaking into the next sprint?"

---

## Scope

- Sprint Health section on Project Detail page (per project)
- Sprint selector dropdown — `Overall` (default, consolidated) + every sprint that has ever existed for the project (`sprint-1`, `sprint-2`, …)
- Per-sprint view (when a specific sprint is selected) — four blocks:
  1. **Epics & Stories** — counts of epics and stories pulled into the sprint, with a list of items carrying multi-sprint labels (carryover signal)
  2. **Planned vs Completed** — counts only, for stories and bugs; epics shown but completion deferred to v1.0+ (see Notes)
  3. **Bugs Logged vs Resolved** — bugs logged in this sprint plus bugs pulled forward from prior sprints; resolved count for the sprint
  4. **End-of-sprint carryover** — counts of bugs, stories, and epics moving forward (multi-label = carryover signal, same mechanism as EP-09 §12.1)
- Consolidated view (`Overall` selected, default) — cross-sprint visualisation of the same four blocks; exact shape PM-deferred
- Sprint Health read model and `GET /projects/{id}/sprint-health` API — single backend endpoint serving both per-sprint and consolidated payloads
- Sprint Health UI section on Project Detail screen

---

## Out of Scope

- Cross-project sprint rollup (per-project only)
- Money / hours / velocity points — count-based metrics only in v1.0 (hours-only across the wider product per ADR-008, but EP-10 starts with counts and adds hours later if needed)
- Sprint retrospective notes or annotations (not requested)
- Editing sprint membership inside Trackwise (GitHub-owned, read-only)
- Drill-down into individual story / bug detail (link out to GitHub for v1.0)
- Real-time updates — same 60s cache as EP-09

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-061 | Sprint Health read model and `GET /projects/{id}/sprint-health` API | 8 |
| ST-062 | Sprint Health UI — per-sprint view with sprint selector dropdown | 5 |
| ST-063 | Sprint Health UI — consolidated `Overall` view across sprints | 5 |

**Total Points:** 18

---

## Dependencies

- ST-045 (sprint label sync) — required for sprint membership and carryover detection
- ST-044 (milestone sync) — epics map to milestones in GitHub; required for the Epic count
- ADR-011 (GitHub Mirror Cache) — same cache layer; no new cache collections required

---

## Assumptions (PM defaults — confirm if a sprint pulls these out)

- **Epic = GitHub milestone**, same as elsewhere in Trackwise. "Epic in sprint" means the milestone has at least one issue carrying the sprint's label
- **"Pulled into sprint"** = work item carries the `sprint-N` label at the time the query runs (GitHub label is the source of truth)
- **"Completed in sprint"** = work item is `closed` in GitHub *and* carries the sprint label. Closing date is not checked against sprint window — append-only label semantics make label presence sufficient
- **"Bug logged in sprint"** = a GitHub issue with `type:bug` label whose `created_at` falls inside the sprint's calendar window
- **"Bug pulled forward"** = a bug labelled with this sprint that was created in a prior sprint window
- **"Resolved"** = GitHub `closed` (QA-verified deferred — QA Reports entity is still parked)
- **Carryover for #4** = same mechanism as EP-09's Sprint Progress tile (multi-label), but extended to include **epics** in this section (EP-09's tile excludes epics; EP-10 includes them because PM explicitly asked for "EPICs moving forward")
- **Sprint calendar window** = derived from milestone due-date or sprint label creation/close timestamps; exact rule to be locked when ST-061 is groomed
- **Sprint dropdown options** = `Overall` + every sprint that has ever existed for the project, no cap

---

## Notes

- EP-09 already has a Sprint Progress tile in the KPI block. EP-10 is the deeper sprint-by-sprint view sitting underneath that tile, not a replacement. EP-09's tile stays as a single-sprint headline; EP-10 is the historical and per-sprint breakdown
- Per-sprint epic completion in #2 (Planned vs Completed) is shown as a count only — epics span sprints by design, so marking them "completed in sprint" is meaningless. The completed count for the epic block in v1.0 will read 0 unless PM later defines "epic completion in sprint" rules
- Consolidated `Overall` view shape is PM-deferred — ST-063 ships a working but minimal version (trend table per sprint × the four blocks) and PM revisits format after first usage
- All numbers are count-based for v1.0. Hours can be layered in if PM asks after first usage
- Work-item vocabulary = **epics / stories / bugs**, consistent with the rest of the project. `type:story`, `type:bug`, and milestone = epic
