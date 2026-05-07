# ST-031 — Forensic one-line indicator on project card

**Story ID:** ST-031
**Epic:** EP-06 — Forensic Summary Integration
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a CTO/CEO, I need to see a one-line forensic health indicator on each project card on the dashboard, so that I can assess scope deviation risk without opening the project detail view.

---

## Acceptance Criteria

- [ ] Each project row on the dashboard includes a forensic indicator line
- [ ] Indicator shows: "CRs: [total] | Pending: [count] ([hours]h at risk) | Absorbed: [count]"
- [ ] If pending count > 0 — pending portion highlighted in Amber
- [ ] If no CR Summary exists — shows "Forensic: Not initiated" in neutral grey
- [ ] Indicator is read-only — clicking does not navigate (detail is in Project Detail view)

---

## Notes

- Depends on ST-029 (CR Summary entity) and ST-030 (data entry) for data to exist
