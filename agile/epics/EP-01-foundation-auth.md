# EP-01 — Foundation & Authentication

**Epic ID:** EP-01
**Title:** Foundation & Authentication
**PRD Reference:** Section 9 (RBAC), Section 6.1 (Organisation)
**Priority:** P0 — Must ship before any other Epic
**Status:** Not Started

---

## Goal

Establish the core application shell, organisation entity, user accounts, and role-based access control. Every other Epic depends on this foundation being in place.

---

## Scope

- Application bootstrap (project setup, routing, DB schema)
- Organisation entity — single org per deployment
- User accounts — creation, login, logout
- RBAC — CTO/CEO and PM roles enforced at route and data level

---

## Out of Scope

- Employee self-service domain knowledge (EP-02)
- Any feature-specific UI beyond auth screens

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-001 | Set up project repository, tech stack, and CI pipeline | 5 |
| ST-002 | Create Organisation entity and seed data | 3 |
| ST-003 | User registration and login | 5 |
| ST-004 | Implement RBAC — CTO/CEO and PM roles | 5 |
| ST-005 | Route and API permission guards by role | 5 |
| ST-050 | Create user flow — direct Cognito creation by Manager | 5 |
| ST-051 | Manage User — reset password (admin-initiated) | 3 |
| ST-052 | Manage User — change user role | 2 |
| ST-053 | Manage User — disable and re-enable user | 3 |
| ST-054 | Manage User — delete user (hard delete) | 3 |

**Total Points:** 39
