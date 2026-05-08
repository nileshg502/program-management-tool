# ST-050 — Create user flow — direct Cognito creation by Manager

**Story ID:** ST-050
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P0
**Points:** 5
**Status:** Not Started

---

## User Story

As a Manager, I need to create a new Trackwise user by entering their name, email, password, and role, so that the new user can sign in and start using the app immediately without an invitation email.

---

## Acceptance Criteria

- [ ] User Management screen exposes a "Create User" action visible to any Manager
- [ ] Create User modal collects: `full_name`, `email`, `initial_password`, `role`
- [ ] `role` selector lists exactly two options: `cto_ceo`, `pm` (display labels: "CTO / CEO", "PM")
- [ ] `initial_password` validated as minimum 8 characters on client and server
- [ ] `email` validated as RFC 5322 format on client and server
- [ ] Submit calls `POST /api/v1/users` with `{ full_name, email, initial_password, role }` (snake_case)
- [ ] Backend invokes Cognito `adminCreateUser` with `MessageAction: SUPPRESS` (no invite email) and a permanent password
- [ ] Cognito user is created with `custom:org_id` set from the calling Manager's JWT context (per ADR-003)
- [ ] User record persisted in Trackwise with: `id`, `cognito_sub`, `full_name`, `email`, `role`, `org_id`, `status` (= `active`), `created_at`, `created_by_user_id`
- [ ] Duplicate email within the same `org_id` returns `409 Conflict` with message "A user with this email already exists"
- [ ] Cognito creation failure rolls back the Trackwise user record (no orphan rows)
- [ ] On success — Create User modal closes and toast displays "User created and active"
- [ ] New user appears in the User Management table immediately with status badge "Active"
- [ ] New user can sign in with the supplied email + password on first attempt — no temporary password / forced reset
- [ ] User Management screen displays for each user: name, email, role, status, last sign-in, created date

---

## Notes

- ADR-003 mandates the SUPPRESS path — no invitation email, password is permanent at creation time.
- "Manager" is the single application role per ADR-003. CTO/CEO and PM are organisational labels stored on the user record but do not gate any permission in v1.0.
- An informational banner inside the modal must communicate "User is created immediately active — no invitation email is sent. Share credentials directly with the user." (matches mockup wording so behaviour is unambiguous to the operator).
- Password complexity beyond the 8-character minimum is governed by the Cognito User Pool password policy and is out of scope for this story.
- This story replaces the legacy self-service "Register" path implied by ST-003 — public registration is not part of v1.0. ST-003 should be re-scoped to "Login + logout" only when this story is scheduled.
