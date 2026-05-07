# ST-026 — PM sets Partially Available flag on an assignment

**Story ID:** ST-026
**Epic:** EP-05 — Team Assignment & Availability
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM, I need to mark a team member as Partially Available, so that management knows this person has capacity and can be considered for additional work.

---

## Acceptance Criteria

- [ ] PM can toggle the `is_partially_available` flag on an active project assignment
- [ ] Toggling updates the employee's availability status in the directory to "Partially Available"
- [ ] Removing the flag returns status to "Assigned"
- [ ] Only the PM assigned to the project (or CTO/CEO) can set this flag — other PMs cannot
- [ ] Flag change is reflected immediately in the availability view (ST-027)
- [ ] `PATCH /api/v1/projects/:project_id/team/:employee_id` accepts `is_partially_available` boolean

---

## Open Question

- Partially Available definition — hours or PM flag? (PRD Open Question #3) — this story implements the PM flag approach as a default; hours-based threshold to be re-evaluated if PM decides differently

---

## Notes

- Flag is per-assignment, not per-employee — the same employee could be Partially Available on one project if they ever have two (though current rule limits to one project at a time)
