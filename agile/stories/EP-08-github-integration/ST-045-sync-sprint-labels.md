# ST-045 — Sync sprint label issues — open vs closed per sprint label

**Story ID:** ST-045
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As the system, I need to sync GitHub issues by sprint label for each project, so that sprint health (committed vs completed) is available without manual entry.

---

## Acceptance Criteria

- [ ] Sprint table created with fields: `id`, `project_id`, `label_name`, `sprint_number`, `sprint_name`, `committed_count`, `completed_count`, `in_progress_count`, `blocked_count`, `last_synced_at`
- [ ] Sync reads all GitHub issues for the project and groups them by sprint label
- [ ] Sprint label pattern is configurable per project — stored as `sprint_label_pattern` on Project entity
- [ ] `committed_count` = total issues with that sprint label
- [ ] `completed_count` = closed issues with that sprint label
- [ ] `in_progress_count` = open issues with sprint label, not labelled "blocked"
- [ ] `blocked_count` = open issues with sprint label AND labelled "blocked"
- [ ] Current sprint = sprint with the highest sprint number that still has open issues
- [ ] `GET /api/v1/projects/:id/sprints/current` returns the current sprint record

---

## Open Question

- Sprint label pattern pending PM decision (PRD Open Question #1) — store as configurable field so it can be set per project without code change

---

## Notes

- Depends on ST-042 (GitHub auth), ST-043 (repo URL), and PRD Open Question #1 resolution
