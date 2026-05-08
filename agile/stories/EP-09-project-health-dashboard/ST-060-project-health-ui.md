# ST-060 — Project Health UI — single-page cockpit screen

**Story ID:** ST-060
**Epic:** EP-09 — Project Health Dashboard
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need a single screen per project showing sprint health, milestone progress, QA results, CR breakdown by origin, and deviation status, so that I can judge a project's overall health in one glance — and re-scope the headline metrics to any past or current sprint via a dropdown — without clicking between tabs.

---

## Acceptance Criteria

### Page structure

- [ ] New screen lives within Project Detail (EP-04) — sits alongside existing tabs (Overview / Team / Forensic-CRs / Alert History / GitHub)
- [ ] Page loads its entire content via a single call to `GET /api/v1/projects/{id}/health` (ST-059)
- [ ] **Header band** — project name, client name, delivery type, go-live date, GitHub repo link, sync status, overall status badge (Green / Amber / Red)

### Sprint scope dropdown (PM-locked design)

- [ ] Dropdown placed near the four KPI tiles (exact placement is UX call; PM scope of the control is fixed)
- [ ] Dropdown options:
  - `All-time` — cumulative project totals
  - `Last 3 sprints` — **default**
  - `Last 6 sprints`
  - `Current sprint`
  - Each historical sprint individually (`Sprint N`, `Sprint N-1`, … `Sprint 1`), most recent first
- [ ] Selecting an option re-scopes **only the four KPI tiles** — Status, Sprint Velocity, Sprint Progress, Open CRs. Tabs and other sections below are unaffected
- [ ] Selection state persists for the user within the session; defaults to `Last 3 sprints` on fresh load

### Four KPI tiles (sprint-scope-aware)

- [ ] **Status tile** — On Track / At Risk / Behind, computed for the selected scope using rules in PRD §12.2
- [ ] **Sprint Velocity tile** — points/sprint, averaged across the selected scope (single value when one sprint is selected; average when a range is selected)
- [ ] **Sprint Progress tile** — completed vs committed counts, plus a carryover line:
  - For sprint-scope: `X completed of Y committed · Z carried from prior sprints`
  - Carryover counts work items (stories + bugs) that already carried a `sprint-K` label where K < selected sprint
  - For all-time scope: total carryover events across project lifetime
- [ ] **Open CRs tile** — count of CRs raised in the selected scope, with pending hours summary; uses the "raised in selected scope" semantic, not "open at end of sprint" (avoids needing historical state)

### Tabs below the KPI tiles (unchanged scope behaviour)

- [ ] Existing project tabs continue to work as today (Overview / Team / Forensic-CRs / Alert History / GitHub) — sprint dropdown does not re-scope tab content in v1.0

### CR section — two tabs (PM-locked design)

- [ ] CR section is split into two sub-tabs:
  - **Deviated CRs** — origin = `deviation` (decisions: absorbed / converted / pending / removed)
  - **Client-Requested CRs** — origin = `client_request` (decisions: approved / rejected / pending / delivered)
- [ ] Each tab shows hour-based KPIs only (no money fields in v1.0):
  - Hours absorbed / converted / pending (Deviated tab)
  - Hours approved / delivered / pending (Client tab)
- [ ] Internal and regulatory CRs treatment — design parked, PM to decide (placeholder area below the two tabs, exact treatment TBD)
- [ ] Sub-tab placement (sub-tabs inside Forensic / CRs vs promote to top-level tabs) — design parked, PM/UX to decide

### Deviation section — running balance per selected scope

- [ ] When a single sprint is selected, deviation panel uses three-column running-balance layout:
  - **Opening balance** (start of sprint): total deviations, count by decision (absorbed / converted with hours / pending)
  - **Activity during sprint**: new deviations detected, decisions made (counts by absorbed / converted with hours / removed)
  - **Closing balance** (end of sprint): total deviations, count by decision
- [ ] When `All-time` is selected, panel collapses to a single column showing project-lifetime totals (no opening / closing concept)
- [ ] When the Deviation entity schema is not yet defined, this section displays "Awaiting deviation schema" and renders no data — does not break the rest of the page

### Other constraints

- [ ] If milestone or sprint data is unavailable (`data_available: false` from API), the affected section shows a neutral inline message without breaking the rest of the page
- [ ] Loading state shows section skeletons; error state for the whole API call shows a single retry affordance
- [ ] Page is responsive at 1280px and above — mobile view is out of scope for v1.0
- [ ] No data on the page is fetched from any source other than the health API — verified in code review

---

## Notes

- The UI is intentionally read-only. Edits to CRs, deviations, QA records, etc. happen on their own screens; this page links out where appropriate but does not host CRUD.
- "Last synced" timestamps are not displayed (per EP-09 scope) — the 60s server-side cache is invisible to the user.
- Work-item vocabulary is **epics / stories / bugs** — no "issues." Sprint metrics count stories + bugs; epics are excluded from sprint-level tiles (epics span sprints by design).
- Origin chips and tab styling are delegated to UX in the next mockup pass.
- Mobile / tablet layout deferred. PM to revisit if field-use scenarios emerge.
- This screen is the v1.0 answer to "how is this project doing right now?" — additional sections (budget burn, team utilisation, blocker list) are deferred unless PM raises them as gaps after first usage.

---

## Parked design decisions (PM to resolve)

The following items were discussed in Session 8 but PM elected to handle internally rather than continue in-session:

- CR sub-tab placement — sub-tabs inside Forensic / CRs vs promote to top-level navigation
- Conversion rate metric — count-based, hour-based, or both shown side-by-side
- Internal + regulatory CR treatment — hidden / small footer / third tab
- Work-item type label convention — `type:story` / bare labels / other
- Whether epics appear in the sprint tile or get a separate epic-progress treatment

These are UI/policy refinements; they do not block ST-060 from entering a sprint, but they should be resolved before the screen is implemented to avoid rework.
