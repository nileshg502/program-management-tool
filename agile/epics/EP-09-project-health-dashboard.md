# EP-09 — Project Health Dashboard

**Epic ID:** EP-09
**Title:** Project Health Dashboard (single-view cockpit)
**PRD Reference:** Section 7 (extension — to be added in next PRD revision)
**Priority:** P1
**Status:** Not Started

---

## Goal

Give PM and CTO/CEO a single page per project that pulls together everything they need to judge "how is this project actually doing right now": sprint health, milestone progress, QA signals, CR mix (with origin breakdown), and deviation counts — all served by one read API and one screen.

---

## Scope

- New CR origin classification — distinguish CRs that originated as deviations from CRs raised directly by the client, plus internal and regulatory origins
- Project Health read model — a single backend object joining sprint, milestone, QA, CR-by-origin, and deviation data for one project
- `GET /api/v1/projects/{id}/health` API — sole data source for the dashboard
- Project Health UI — single-page cockpit, section per domain, fully driven by the health API
- Data ownership boundary enforced — frontend reads only Trackwise APIs; backend may call GitHub server-side with short-TTL cache for milestone and sprint membership data

---

## Out of Scope

- Real-time push / webhook updates from GitHub (deferred — short-TTL cache is sufficient for v1.0)
- Manual "refresh now" button on the dashboard (deferred)
- "Last synced" UI element (not needed — backend cache TTL is short and invisible to user)
- Cross-project portfolio view (already covered by EP-03 client dashboard)
- Drill-down into individual CR records (covered by EP-06)
- Editing sprint or milestone data inside Trackwise (those remain GitHub-owned, read-only in Trackwise)

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-058 | CR Summary — add origin classification and source deviation link | 3 |
| ST-059 | Project Health read model and `GET /projects/{id}/health` API | 8 |
| ST-060 | Project Health UI — single-page cockpit screen | 8 |

**Total Points:** 19

---

## Dependencies

- ST-029 (CR Summary entity) — ST-058 extends it
- ST-030 (Manual CR entry) — UI must let PM set `origin` when creating/editing a CR
- ST-019 (Milestone status calculation) — feeds the milestone section
- ST-020 (Sprint health) — feeds the sprint section
- PRD Open Questions #1 (sprint label convention) and #2 (At Risk threshold) — gate ST-059's sprint and risk sections

---

## Blocked-by — structure definition (PM-owned)

The dashboard reads two domains for which **no entity exists in the backlog yet**:

- **QA Reports** — pass / fail / defects per sprint. No table, no entry form, no story.
- **Deviations** — scope deviation records that may be converted to CRs. No table, no entry form, no story. ST-058's `source_deviation_id` field references this entity.

PM has parked the schema decision. Two paths will resolve it:

1. **PM defines the structure** — fields, validations, lifecycle states — then stories are written to build the table and entry form
2. **PM provides an existing DB** — Trackwise builds against that schema; missing fields are added in-place

Until one of these resolves, **ST-059 cannot return live QA or Deviation data** for the dashboard. The API may stub these sections (return `data_available: false` with reason `"awaiting schema"`) so ST-060 can still ship a complete UI shell, or the affected sections can be deferred. PM decision required at sprint planning time.

Per ADR-008, whichever path is chosen, both entities will be Trackwise-owned (CRUD) and stored in the Trackwise database. No external sources.

---

## Notes

- All data displayed on this dashboard must be stored in the Trackwise database. Read-only mirroring is acceptable for milestone and sprint membership (sourced from GitHub server-side); everything else is CRUD-owned by Trackwise.
- ADR-008 (to be drafted) codifies the data ownership and sync rules referenced above.
- This epic is the user-visible payoff for ST-029 / ST-030 / ST-058 — without it, CR origin classification has no surface in the product.
- Work-item vocabulary is **epics / stories / bugs** — no "issues." Sprint metrics count stories + bugs; epics are excluded from sprint-level tiles (epics span sprints by design).

---

## Design — locked vs parked (Session 8 working state)

PM and Claude worked through the dashboard design in Session 8 (2026-05-08). The locked items are reflected in ST-059 and ST-060 acceptance criteria. The parked items are listed for follow-up.

### Locked

- **No money tracking in v1.0** — hours-only across all CR and deviation views. No rate, no revenue, no billed/unbilled status
- **CR section split into two tabs:**
  - Deviated CRs (origin = `deviation`) — decisions: absorbed / converted / pending / removed
  - Client-Requested CRs (origin = `client_request`) — decisions: approved / rejected / pending / delivered
- **Decision vocabulary differs per origin** — single `decision` field on the CR record, validated against `origin`
- **Sprint scope dropdown** — re-scopes only the four KPI tiles (Status / Sprint Velocity / Sprint Progress / Open CRs); does not re-scope tabs or other sections
  - Options: All-time, Last 3 sprints (default), Last 6 sprints, Current sprint, each historical sprint individually
  - Placement near the KPI tiles (UX call within that constraint)
- **Carryover line on Sprint Progress tile** — counts stories + bugs that carry a sprint label from any prior sprint; epics excluded
- **Carryover detection mechanism** — GitHub labels are append-only. A work item retains every `sprint-K` label it has ever held. Multi-label = carryover signal. PRD §12.1 must explicitly state this rule (PM follow-up)
- **Deviation section uses running-balance layout** when a single sprint is selected (opening balance / activity during sprint / closing balance); collapses to single-column totals for `All-time`
- **Open CRs tile uses "raised in selected scope" semantic** — avoids needing historical state / audit log for v1.0

### Parked — PM to resolve

- **CR sub-tab placement** — sub-tabs inside Forensic / CRs vs promoted to top-level navigation
- **Conversion rate metric** — count-based (Converted CRs ÷ Total) vs hour-based (Hours Converted ÷ Total Hours) vs both shown side-by-side
- **Internal + regulatory CR treatment** — hidden / small footer below the two tabs / third tab
- **Work-item type label convention** — `type:story` / bare labels (`story`, `bug`, `epic`) / something else
- **Epic display in sprint metrics** — confirm epics are excluded from the sprint tile (current assumption) and given a separate epic-progress treatment elsewhere, or pulled into the sprint tile

### Parked — schema definition (covered by "Blocked-by — structure definition" above)

- **QA Report entity** — schema and entry path
- **Deviation entity** — schema and entry path

These items do not block ST-059 / ST-060 from entering a sprint, but they should be resolved before implementation begins to avoid rework. PM elected to handle these internally rather than continue in-session resolution.
