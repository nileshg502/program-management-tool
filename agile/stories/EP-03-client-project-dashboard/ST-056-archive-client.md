# ST-056 — Manage Client — archive client

**Story ID:** ST-056
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As a Manager, I need to archive a client when the engagement has ended, so that the client and its projects no longer clutter active dashboards while remaining available for historical reference.

---

## Acceptance Criteria

- [ ] Manage Client drawer (S-16) exposes an "Archive client" action in the Danger zone
- [ ] Action calls `POST /api/v1/clients/:id/archive`
- [ ] Backend sets `clients.status = archived` and `clients.archived_at = NOW()`
- [ ] All projects under the archived client are cascade-set to `projects.status = archived`
- [ ] Archived client does NOT appear on the CTO/CEO portfolio dashboard (EP-03 client cards)
- [ ] Archived client does NOT appear in the active filter on Client Management — appears under an "Archived" filter chip
- [ ] Archived client appears in `GET /api/v1/clients?status=archived`
- [ ] Existing project detail views for archived projects render as read-only — no add / edit / delete actions exposed; "Archived" banner shown at the top
- [ ] GitHub sync is paused for archived projects — no new milestone or sprint data is fetched
- [ ] Existing forensic CR records on archived projects remain intact and viewable in read-only form
- [ ] Active employee assignments on archived projects are preserved but the project disappears from the employee's "Current project" cell — bench / availability views recalculate
- [ ] Archive is reversible — an "Unarchive client" action appears on archived clients in the drawer; calls `POST /api/v1/clients/:id/unarchive` which sets `status = active` on the client only (projects remain archived until explicitly unarchived per project)
- [ ] On success — drawer closes, client disappears from active table, and toast displays "Client archived — no longer visible on dashboard"
- [ ] Activity entry written: `{ action: "client_archived", project_count_at_archive, performed_by_user_id, performed_at }`

---

## Notes

- Archive is non-destructive. Hard deletion is covered by ST-057.
- Cascade rule: archive at client level cascades to projects but NOT in reverse — archiving a single project does not archive the client.
- Pausing GitHub sync prevents background cost on dead engagements; the sync scheduler must respect `projects.status` per ADR-004.
- Existing assignments are preserved so the employee's project history remains accurate. The bench view treats archived projects as "not current" — equivalent to having no current project.
- This story replaces the lighter "deactivate" behaviour described in ST-011's acceptance criteria. ST-011 should be updated to reference `archive` semantics when this story is scheduled.
