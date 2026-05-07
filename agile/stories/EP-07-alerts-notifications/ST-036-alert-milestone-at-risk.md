# ST-036 — Alert trigger — milestone status change to At Risk

**Story ID:** ST-036
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM, I need to receive an alert when a milestone moves to At Risk, so that I have an early warning before the milestone becomes Behind.

---

## Acceptance Criteria

- [ ] When a milestone status changes to `at_risk` — alert is created for the PM assigned to the project only
- [ ] CTO/CEO does not receive the At Risk alert — they receive Behind only (ST-035)
- [ ] Alert message: "Milestone [name] on [project name] is At Risk. Due: [date]. % complete: [x]%"
- [ ] Alert only triggered on status transition — not re-triggered on every sync while still At Risk
- [ ] If milestone moves to Behind later — ST-035 trigger fires instead; no duplicate At Risk alert
- [ ] Unit test: On Track → At Risk creates alert; At Risk → At Risk does not; At Risk → Behind triggers ST-035 only

---

## Notes

- Depends on ST-034 (Alert entity) and ST-019 (status calculation event emission)
