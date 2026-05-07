# ST-022 — Forensic summary section — CR totals by decision type

**Story ID:** ST-022
**Epic:** EP-04 — Project Detail View
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see a forensic summary on the project detail page showing CR counts and hours by decision type, so that I can assess scope deviation risk at a glance.

---

## Acceptance Criteria

- [ ] Forensic summary section displays: total CRs logged, absorbed count + hours, converted count, pending count + hours at risk, removed count
- [ ] Data sourced from CR Summary entity (EP-06 ST-029)
- [ ] If no CR Summary exists for the project — section shows "Forensic tracking not initiated"
- [ ] Pending count highlighted in Amber if count > 0
- [ ] Section is read-only — CR Summary update is done via EP-06 ST-030

---

## Notes

- Depends on EP-06 for CR Summary entity and data to exist
