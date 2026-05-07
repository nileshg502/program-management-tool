# ST-044 — Sync GitHub milestones — name, due date, issue counts

**Story ID:** ST-044
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As the system, I need to sync milestones from GitHub for each project, so that milestone health data is available without manual entry.

---

## Acceptance Criteria

- [ ] Milestone table created with fields: `id`, `project_id`, `github_milestone_number`, `name`, `due_date`, `open_issues_count`, `closed_issues_count`, `state` (open/closed), `last_synced_at`
- [ ] Sync reads all milestones from the project's GitHub repo via GitHub API
- [ ] New milestones are inserted; existing milestones are updated; milestones deleted from GitHub are marked closed
- [ ] `due_date` maps from GitHub milestone `due_on` field — null if not set
- [ ] `open_issues_count` and `closed_issues_count` map from GitHub milestone response
- [ ] `last_synced_at` updated on every successful sync
- [ ] Sync error stored on the project — does not crash the sync for other projects
- [ ] `GET /api/v1/projects/:id/milestones` returns all milestones for a project

---

## Notes

- % complete = `closed_issues_count / (open_issues_count + closed_issues_count) × 100` — calculated on read, not stored
- Depends on ST-042 (GitHub auth) and ST-043 (repo URL on project)
