# ST-009 — People directory list view for management

**Story ID:** ST-009
**Epic:** EP-02 — People Directory
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to view the full organisation people directory, so that I can see who works here, what roles they hold, what domains they know, and whether they are available.

---

## Acceptance Criteria

- [ ] Directory page lists all active employees
- [ ] Each employee row shows: name, role, domain knowledge (comma-separated list), availability status
- [ ] Availability status derived from `current_project_id`: null = Available, set = Assigned, PM flag = Partially Available
- [ ] Clicking an employee opens their profile — full domain knowledge list with years of experience
- [ ] Directory is read-only for management — no edit controls shown
- [ ] Empty state shown when no employees exist yet
- [ ] Page accessible to CTO/CEO and PM roles — denied for unauthenticated or employee-only access

---

## Notes

- Availability status display is read-only here; assignment and Partially Available flag are managed in EP-05
