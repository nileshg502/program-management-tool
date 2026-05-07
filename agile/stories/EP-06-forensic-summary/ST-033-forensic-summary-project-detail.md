# ST-033 — Forensic summary display on project detail view

**Story ID:** ST-033
**Epic:** EP-06 — Forensic Summary Integration
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see the full forensic summary on the project detail view, so that I can review all CR counts, hours at risk, and the last-updated timestamp in one place.

---

## Acceptance Criteria

- [ ] Forensic summary section on project detail page shows: total CRs, absorbed (count + hours), converted count, pending (count + hours at risk), removed count, last updated timestamp
- [ ] Pending row highlighted in Amber if pending count > 0
- [ ] Last updated timestamp formatted as readable date and time
- [ ] PM sees an "Update forensic summary" button — opens CR Summary form (ST-030)
- [ ] CTO/CEO sees data read-only — no update button
- [ ] If no CR Summary — shows "Forensic tracking not initiated" with initiate prompt for PM (ST-032)

---

## Notes

- This story is the fuller version of the one-line indicator in ST-031 — both use the same CR Summary data source
