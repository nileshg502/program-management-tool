# ST-003 — User registration and login

**Story ID:** ST-003
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P0
**Points:** 5
**Status:** Not Started

---

## User Story

As a user, I need to register an account and log in with my email and password, so that I can access the application securely.

---

## Acceptance Criteria

- [ ] User can register with name, email, and password
- [ ] Password must be minimum 8 characters — validation enforced on client and server
- [ ] Duplicate email registration rejected with clear error message
- [ ] User can log in with email and password
- [ ] Incorrect credentials return a generic error — no field-level hint (security)
- [ ] Session token issued on login — stored securely (httpOnly cookie or equivalent)
- [ ] User can log out — session token invalidated server-side
- [ ] Unauthenticated requests to protected routes redirect to login
- [ ] User record contains: `id`, `name`, `email`, `role`, `created_at`

---

## Notes

- Passwords must be hashed (bcrypt or equivalent) — plain text storage is not acceptable
- Role is assigned by admin after registration — default role is pending/no-access until assigned
- Password reset flow is not in scope for ST-003 — separate story if needed
