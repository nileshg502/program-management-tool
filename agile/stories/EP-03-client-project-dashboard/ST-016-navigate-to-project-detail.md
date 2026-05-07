# ST-016 — Navigate from project row to Project Detail view

**Story ID:** ST-016
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 2
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to click a project on the dashboard and land on its Project Detail view, so that I can drill into health, team, and forensic data for that specific project.

---

## Acceptance Criteria

- [ ] Each project row on the dashboard is clickable
- [ ] Clicking navigates to the Project Detail page at route `/projects/:id`
- [ ] Browser back button returns to the dashboard at the previous scroll position
- [ ] Project Detail page is accessible directly via URL — no dashboard visit required
- [ ] If PM navigates directly to a project they are not assigned to, they receive a 403 / "Access Denied" screen
- [ ] Page title updates to the project name on the Project Detail view

---

## Notes

- Project Detail view content is built in EP-04
