# ST-049 — Sync failure warning displayed on project

**Story ID:** ST-049
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see a clear warning when GitHub sync has failed for a project, so that I know the data I am looking at may be stale and can investigate.

---

## Acceptance Criteria

- [ ] If the last sync for a project resulted in an error — a warning banner is shown on the project detail page
- [ ] Warning shows: "GitHub sync failed. Last successful sync: [date/time]. Error: [message]"
- [ ] Warning also shown on the project row on the dashboard (icon or label)
- [ ] Warning automatically clears when a successful sync completes
- [ ] If GitHub integration is not configured — shows "GitHub integration not configured" (neutral, not a warning)
- [ ] If sync has never run — shows "GitHub sync not yet run" (neutral)

---

## Notes

- Sync error message from GitHub API should be sanitised before display — do not expose raw API errors to end users
