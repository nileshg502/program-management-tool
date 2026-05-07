# ST-048 — Manual refresh trigger per project

**Story ID:** ST-048
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to trigger a manual GitHub sync for a project, so that I can get the latest data immediately without waiting for the scheduled sync.

---

## Acceptance Criteria

- [ ] "Refresh" button available on the project detail page
- [ ] Clicking refresh triggers an immediate sync for that project only
- [ ] Button shows a loading state while sync is in progress
- [ ] On success — page data refreshes and "Last synced: [time]" timestamp updates
- [ ] On failure — error message displayed inline; button returns to default state
- [ ] Refresh is rate-limited — user cannot trigger more than once per minute per project
- [ ] `POST /api/v1/projects/:id/sync` triggers the sync and returns job status

---

## Notes

- Manual refresh shares the same sync logic as the scheduled job (ST-047) — not a separate code path
