# EP-04 — Project Detail View

**Epic ID:** EP-04
**Title:** Project Detail View
**PRD Reference:** Section 7.3
**Priority:** P1
**Status:** Not Started

---

## Goal

Give PM and management a full health view of a single project — overview, milestone health, sprint health, team composition, and forensic summary — in one screen.

---

## Scope

- Project overview section — client, name, code, delivery type, go-live, current milestone
- Milestone health section — list of milestones with name, due date, % complete, status
- Sprint health section — current sprint name/number, stories committed vs completed, in-progress, blocked count
- Team section — people assigned with role and domain knowledge
- Forensic summary section — CR counts and hours by decision type
- Milestone and sprint data pulled from GitHub (depends on EP-08)

---

## Out of Scope

- Editing project details from this view (admin function)
- Forensic CR detail (links to forensic documents, not in-app)
- Team assignment actions (EP-05)

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-017 | Project overview section — static fields display | 3 |
| ST-018 | Milestone health section — list with status per milestone | 5 |
| ST-019 | Milestone status calculation — On Track / At Risk / Behind | 8 |
| ST-020 | Sprint health section — committed vs completed vs blocked | 5 |
| ST-021 | Team section — assigned people with role and domain | 3 |
| ST-022 | Forensic summary section — CR totals by decision type | 3 |

**Total Points:** 27
