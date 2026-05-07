# ST-038 — Alert trigger — forensic CR Pending for more than 1 sprint

**Story ID:** ST-038
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM, I need to receive an alert when a forensic CR has been in Pending state for more than one sprint, so that I follow up on unresolved scope decisions before they accumulate.

---

## Acceptance Criteria

- [ ] CR Summary record stores `oldest_pending_cr_sprint` — the sprint number when the oldest pending CR was first logged
- [ ] After every sprint boundary (or on demand), check: current sprint number − `oldest_pending_cr_sprint` > 1
- [ ] If condition is true and pending count > 0 — alert created for PM
- [ ] Alert message: "Project [name] has [n] pending CRs. The oldest has been pending since Sprint [n]."
- [ ] Alert not re-triggered every sprint — only when pending age increases past a new whole-sprint boundary
- [ ] PM can dismiss the alert — dismissal does not resolve the CR, just clears the notification

---

## Notes

- Sprint number tracking for oldest pending CR is a PM-maintained field on CR Summary — same manual approach as ST-030
- Depends on ST-029 (CR Summary entity) and ST-034 (Alert entity)
