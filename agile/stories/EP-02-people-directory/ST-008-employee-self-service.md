# ST-008 — Employee self-service — add and edit own domain knowledge

**Story ID:** ST-008
**Epic:** EP-02 — People Directory
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As an employee, I need to add, edit, and remove my own domain knowledge entries, so that the directory accurately reflects my expertise without depending on management to update it for me.

---

## Acceptance Criteria

- [ ] Employee can log in and access their own profile page
- [ ] Employee can add a new domain knowledge entry (domain name + years of experience)
- [ ] Employee can edit an existing domain knowledge entry they own
- [ ] Employee can remove a domain knowledge entry they own
- [ ] Employee cannot view or edit another employee's domain knowledge
- [ ] Management (CTO/CEO, PM) cannot create or edit domain knowledge entries — read only
- [ ] Changes are saved immediately — no approval workflow
- [ ] API enforces ownership: `POST/PATCH/DELETE /api/v1/employees/:id/domain-knowledge` returns `403` if caller is not the employee themselves

---

## Notes

- This requires a third user role at the system level: `employee` — not in PRD RBAC table but implied by self-service requirement
- Confirm with PM before implementing whether employees have system logins or if self-service is via a separate access mechanism
