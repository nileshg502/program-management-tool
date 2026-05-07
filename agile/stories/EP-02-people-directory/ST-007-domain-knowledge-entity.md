# ST-007 — Create Domain Knowledge entity linked to Employee

**Story ID:** ST-007
**Epic:** EP-02 — People Directory
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As the system, I need a Domain Knowledge entity that records which industry domains an employee has experience in and for how many years, so that management can find the right people for a project.

---

## Acceptance Criteria

- [ ] Domain Knowledge table created with fields: `id`, `employee_id`, `domain_name`, `years_of_experience`, `created_at`, `updated_at`
- [ ] An employee can have zero or more domain knowledge entries
- [ ] `domain_name` is free text — no fixed enum (allows new domains to be added without schema change)
- [ ] `years_of_experience` is a positive integer
- [ ] Domain knowledge entries returned as a list when fetching an employee record
- [ ] `GET /api/v1/employees/:id/domain-knowledge` returns all entries for an employee

---

## Notes

- Domain knowledge is self-declared by the employee (enforced in ST-008)
- Management can view but cannot create or edit domain knowledge entries (enforced in ST-008)
