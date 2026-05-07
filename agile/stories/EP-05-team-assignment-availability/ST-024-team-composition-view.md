# ST-024 — Show team composition — role and domain per member

**Story ID:** ST-024
**Epic:** EP-05 — Team Assignment & Availability
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to see the full team composition for a project — who is on it, what role each person holds, and what domain knowledge they bring — so that I can make informed staffing decisions.

---

## Acceptance Criteria

- [ ] Team management screen lists all currently assigned employees for a project
- [ ] Each row shows: employee name, role, domain knowledge list, availability flag (Partially Available if set)
- [ ] Team list is sorted by role — Architect, PM, BA, Engineer, QA, DevOps
- [ ] Screen includes an "Add member" action (triggers ST-023 assignment flow)
- [ ] Screen includes a "Remove" action per row (triggers ST-023 remove flow)
- [ ] Screen accessible to CTO/CEO (all projects) and PM (own projects only)

---

## Notes

- This is the management surface for team composition — the read-only view on Project Detail (ST-021) uses the same data
