# ST-037 — Alert trigger — sprint blocked stories exceed 3

**Story ID:** ST-037
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM, I need to receive an alert when a sprint has more than 3 blocked stories, so that I can intervene before blockers stall the sprint.

---

## Acceptance Criteria

- [ ] After every GitHub sync, blocked issue count is checked per project's current sprint
- [ ] If blocked count transitions from ≤3 to >3 — alert created for PM of that project
- [ ] Alert message: "Sprint [name] on [project name] has [n] blocked stories."
- [ ] Alert not re-triggered on subsequent syncs while count remains >3
- [ ] If count drops to ≤3 and rises again — alert re-triggers
- [ ] Unit test: count goes 3→4 creates alert; stays at 4 does not; 4→3→4 creates a new alert

---

## Notes

- Threshold is fixed at >3 in v1.0 — configurable threshold is out of scope
- Depends on ST-034 (Alert entity) and EP-08 (GitHub sync for blocked label count)
