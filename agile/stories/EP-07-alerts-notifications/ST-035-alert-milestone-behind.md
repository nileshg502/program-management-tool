# ST-035 — Alert trigger — milestone status change to Behind

**Story ID:** ST-035
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO and PM, I need to receive an alert when a milestone moves to Behind status, so that I am immediately aware of a delivery risk without checking the dashboard manually.

---

## Acceptance Criteria

- [ ] When a milestone status changes to `behind` — alert is created for both CTO/CEO (all users with that role) and the PM assigned to the project
- [ ] Alert message: "Milestone [name] on [project name] is now Behind. Due: [date]. % complete: [x]%"
- [ ] Alert is only triggered on status transition — not re-triggered on every subsequent sync while still Behind
- [ ] If milestone recovers to At Risk or On Track and falls back to Behind later — alert is re-triggered
- [ ] Alert created via the internal event emitted by ST-019 (milestone status calculation)
- [ ] Unit test: status transition from On Track → Behind creates alert; staying Behind does not create a duplicate

---

## Notes

- "PM assigned to the project" means the user with role `pm` who is assigned to the project — not all PMs in the system
