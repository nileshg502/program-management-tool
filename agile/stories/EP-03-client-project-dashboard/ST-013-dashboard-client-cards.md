# ST-013 — Dashboard — client card list with project rows

**Story ID:** ST-013
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO, I need a top-level dashboard showing all clients and their active projects, so that I can see the full portfolio at a glance without navigating into each project individually.

---

## Acceptance Criteria

- [ ] Dashboard is the default landing page for CTO/CEO role after login
- [ ] Each client appears as a card
- [ ] Each client card shows: client name, industry, number of active projects, overall status badge
- [ ] Under each client card, active projects are listed as rows
- [ ] Each project row shows: project name, current milestone name, milestone due date, milestone status badge, team size
- [ ] If a client has no active projects, the client card still appears with "No active projects"
- [ ] Dashboard is accessible to CTO/CEO only — PM sees only their own projects (separate view or filtered)
- [ ] Page loads within 2 seconds for up to 20 clients and 50 projects

---

## Notes

- Milestone data depends on EP-08 (GitHub sync) — until EP-08 is done, milestone fields show "Not synced"
- Team size is count of employees currently assigned to the project
