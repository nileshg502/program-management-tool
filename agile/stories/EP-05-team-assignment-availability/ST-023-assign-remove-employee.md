# ST-023 — Assign and remove employee from a project

**Story ID:** ST-023
**Epic:** EP-05 — Team Assignment & Availability
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to assign employees to a project and remove them when they roll off, so that the system always reflects the current team composition.

---

## Acceptance Criteria

- [ ] Project Assignment table created with fields: `id`, `project_id`, `employee_id`, `assigned_at`, `removed_at` (nullable), `is_partially_available`
- [ ] Management can assign an employee to a project from the project detail or team management screen
- [ ] Management can remove an employee from a project — `removed_at` set, record not deleted
- [ ] An employee can only be assigned to one project at a time — assigning to a second project prompts confirmation or blocks
- [ ] Removing an employee updates their availability status to Available in the directory
- [ ] `GET /api/v1/projects/:id/team` returns all currently active assignments for the project
- [ ] Assignment action is available to CTO/CEO and PM (own projects only)

---

## Notes

- Historical assignments are retained for audit — only active assignments (removed_at is null) count toward availability
- Confirmation prompt when assigning someone already on another project: "This employee is currently assigned to [Project X]. Reassign?"
