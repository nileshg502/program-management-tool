# ST-029 — Create CR Summary entity linked to Project

**Story ID:** ST-029
**Epic:** EP-06 — Forensic Summary Integration
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As the system, I need a CR Summary entity that stores aggregate forensic tracking data per project, so that forensic health can be displayed on the dashboard and project detail view without querying raw CR documents.

---

## Acceptance Criteria

- [ ] CR Summary table created with fields: `id`, `project_id`, `total_crs`, `absorbed_count`, `absorbed_hours`, `converted_count`, `pending_count`, `pending_hours_at_risk`, `removed_count`, `last_updated_at`
- [ ] One CR Summary record per project — not one per sprint
- [ ] `project_id` has a unique constraint — only one summary per project
- [ ] All count and hours fields default to 0
- [ ] `GET /api/v1/projects/:id/cr-summary` returns the CR Summary for a project
- [ ] Returns 404 with `{ "forensic_tracking": "not_initiated" }` if no CR Summary exists for the project

---

## Notes

- CR Summary is a manually maintained aggregate in v1.0 — populated via ST-030
- Automated sync from cr-tracker.md files is deferred to a future version
