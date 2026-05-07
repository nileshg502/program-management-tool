# ST-005 — Route and API permission guards by role

**Story ID:** ST-005
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P0
**Points:** 5
**Status:** Not Started

---

## User Story

As the system, I need all routes and API endpoints to enforce role-based access, so that a PM cannot access CTO/CEO-only views and a CTO/CEO cannot access PM-restricted data of other PMs.

---

## Acceptance Criteria

- [ ] Frontend routes protected — unauthenticated user redirected to login
- [ ] Frontend routes protected by role — wrong role redirected to "Access Denied" screen
- [ ] All API endpoints return `401 Unauthorized` for unauthenticated requests
- [ ] All API endpoints return `403 Forbidden` for authenticated requests with insufficient role
- [ ] PM can only access projects they are assigned to — other projects return `403`
- [ ] CTO/CEO can access all projects and all clients
- [ ] Permission check is server-side — client-side hiding is UI only, not a security boundary
- [ ] Guard logic is centralised — not duplicated per route/endpoint

---

## Notes

- "PM assigned to project" relationship must be established before this story is testable end-to-end — coordinate with EP-03 / EP-05
