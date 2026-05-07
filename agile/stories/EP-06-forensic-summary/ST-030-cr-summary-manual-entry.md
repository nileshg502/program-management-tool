# ST-030 — Manual CR Summary entry and update by PM

**Story ID:** ST-030
**Epic:** EP-06 — Forensic Summary Integration
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM, I need to enter and update the CR Summary figures for my project, so that the forensic health displayed in Trackwise reflects the current state of the cr-tracker document.

---

## Acceptance Criteria

- [ ] PM can open a CR Summary update form for their project
- [ ] Form fields: total CRs, absorbed count, absorbed hours, converted count, pending count, pending hours at risk, removed count
- [ ] All fields are numeric — non-numeric input rejected with validation message
- [ ] Negative values rejected
- [ ] Submitting the form updates (or creates) the CR Summary record for the project
- [ ] `last_updated_at` timestamp updated on every save
- [ ] CTO/CEO can view but not edit the CR Summary (read only for CTO/CEO)
- [ ] `PUT /api/v1/projects/:id/cr-summary` accepts all summary fields

---

## Notes

- No automated validation against actual CR documents — PM is responsible for accuracy
- `last_updated_at` is displayed on the forensic summary section so management knows how fresh the data is
