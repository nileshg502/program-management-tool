# ST-041 — Alert history view per project

**Story ID:** ST-041
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see the full alert history for a project, so that I can review what issues were flagged and when.

---

## Acceptance Criteria

- [ ] Alert history is accessible from the project detail page
- [ ] List shows all alerts ever generated for the project — both read and unread
- [ ] Each row shows: alert type, message, date/time generated, read/unread status
- [ ] List ordered newest first
- [ ] Filtered to the authenticated user's alerts — PM sees own alerts, CTO/CEO sees CTO/CEO alerts
- [ ] Empty state shown if no alerts have been generated for the project
- [ ] `GET /api/v1/projects/:id/alerts` returns paginated alert history for the project and the authenticated user

---

## Notes

- No deletion or archival of alerts in v1.0
