# ST-020 — Sprint health section — committed vs completed vs blocked

**Story ID:** ST-020
**Epic:** EP-04 — Project Detail View
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see the current sprint's health — how many stories were committed, completed, in progress, and blocked — so that I can assess delivery velocity and identify blockers.

---

## Acceptance Criteria

- [ ] Sprint health section shows the current sprint (highest sprint number with open issues)
- [ ] Section displays: sprint name/number, stories committed, stories completed, stories in progress, blocked story count
- [ ] Stories committed = all issues with the current sprint label
- [ ] Stories completed = closed issues with the current sprint label
- [ ] Stories in progress = open issues with current sprint label, not labelled "blocked"
- [ ] Blocked = open issues with current sprint label AND labelled "blocked"
- [ ] If no sprint label data available — section shows "No sprint data found"
- [ ] If GitHub not connected — section shows "GitHub integration not configured"
- [ ] Data sourced from EP-08 sync

---

## Open Question

- Sprint label naming convention is pending PM decision (PRD Open Question #1) — story assumes label format TBD; implementation must be configurable per project

---

## Notes

- Sprint label convention must be resolved before this story can be fully implemented
