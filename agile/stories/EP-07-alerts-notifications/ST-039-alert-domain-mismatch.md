# ST-039 — Alert trigger — team domain knowledge mismatch on project

**Story ID:** ST-039
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM, I need to receive an alert when a team member has no domain knowledge matching any of the project's required domains, so that I can address skill gaps proactively.

---

## Acceptance Criteria

- [ ] When a domain gap is detected (EP-05 ST-025 logic) — alert is created for the PM
- [ ] Alert message: "Domain gap on [project name]: no team member covers [domain name]."
- [ ] One alert per gap per project — if two domains are missing, two alerts are created
- [ ] Alert not re-triggered on every check if gap already reported — only on new gaps
- [ ] If gap is resolved (team member with the domain is added) — no further alert for that domain until it opens again
- [ ] Depends on ST-025 (domain mismatch detection) and ST-034 (Alert entity)

---

## Notes

- Alert is PM-only — CTO/CEO can see the mismatch flag on the project detail view but does not receive an alert for it
