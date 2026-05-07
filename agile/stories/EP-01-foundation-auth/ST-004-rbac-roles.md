# ST-004 — Implement RBAC — CTO/CEO and PM roles

**Story ID:** ST-004
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P0
**Points:** 5
**Status:** Not Started

---

## User Story

As an admin, I need to assign CTO/CEO or PM roles to users, so that the application enforces the correct access levels defined in the PRD.

---

## Acceptance Criteria

- [ ] Two roles available: `cto_ceo` and `pm`
- [ ] Admin can assign a role to any user via admin screen
- [ ] Admin can change a user's role
- [ ] Role stored on the user record
- [ ] Logged-in user's role is available in the session/token
- [ ] Role is returned in `GET /api/v1/me` response
- [ ] Each role maps to the permissions defined in PRD Section 9:
  - `cto_ceo` — view all clients, all projects, portfolio dashboard, people directory, forensic summary, all alerts
  - `pm` — view own projects only, project detail, team assignment, people directory, forensic summary, own project alerts

---

## Notes

- "Employee" is a separate entity from "User" — an employee who self-manages domain knowledge does not need a system login
- Admin role is a bootstrap concern — not exposed as a PRD feature
