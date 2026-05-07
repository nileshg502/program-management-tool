# ST-006 — Create Employee entity and admin CRUD

**Story ID:** ST-006
**Epic:** EP-02 — People Directory
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As an admin, I need to create and manage employee records, so that the organisation's people directory is populated with the full workforce.

---

## Acceptance Criteria

- [ ] Employee table created with fields: `id`, `name`, `role`, `current_project_id` (nullable), `created_at`, `updated_at`
- [ ] Role field accepts: `Engineer`, `QA`, `BA`, `Architect`, `DevOps`, `PM`
- [ ] Admin can create a new employee record
- [ ] Admin can edit employee name and role
- [ ] Admin can deactivate (soft delete) an employee — record retained for history
- [ ] Deactivated employees do not appear in directory or assignment views
- [ ] `GET /api/v1/employees` returns all active employees
- [ ] `GET /api/v1/employees/:id` returns a single employee with domain knowledge list

---

## Notes

- Employee is distinct from User (system login) — an employee may not have a login account
- current_project_id is managed by the assignment flow (EP-05), not directly here
