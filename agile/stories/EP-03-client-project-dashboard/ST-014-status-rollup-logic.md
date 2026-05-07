# ST-014 — Status roll-up logic — worst status across projects per client

**Story ID:** ST-014
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO, I need the client card to show the worst health status across all of that client's active projects, so that I can immediately identify which clients need attention without reading every project row.

---

## Acceptance Criteria

- [ ] Status priority order (worst to best): Behind > At Risk > On Track
- [ ] Client overall status = worst milestone status across all its active projects
- [ ] If any project is Behind — client card shows Behind (Red)
- [ ] If no project is Behind but any is At Risk — client card shows At Risk (Amber)
- [ ] If all projects are On Track — client card shows On Track (Green)
- [ ] If no milestone data available (GitHub not yet synced) — client card shows "No data" in neutral colour
- [ ] Roll-up recalculates automatically whenever a project's milestone status changes
- [ ] Logic is server-side — not computed in the frontend

---

## Notes

- Status roll-up depends on milestone status values populated by EP-08 (GitHub sync) and EP-04 ST-019 (status calculation)
