# ST-047 — Scheduled auto-sync every 15 minutes

**Story ID:** ST-047
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As the system, I need GitHub data to refresh automatically every 15 minutes, so that milestone and sprint health is current without requiring manual intervention.

---

## Acceptance Criteria

- [ ] Background job runs every 15 minutes and triggers sync for all active projects with a configured GitHub repo URL
- [ ] Each project is synced independently — one project failing does not block others
- [ ] Sync job logs start time, end time, and success/failure per project
- [ ] If a project sync fails — `sync_error` field updated on the project with the error message and timestamp
- [ ] If GitHub API rate limit is hit — sync backs off and retries after the rate limit reset time
- [ ] Job schedule is configurable via environment variable — default 15 minutes
- [ ] Sync can be disabled per project (for archived projects)

---

## Notes

- Job scheduler implementation is an Engineering/Architect decision (cron, queue, scheduler library)
- Rate limit for GitHub API is 5000 requests/hour authenticated — for large deployments, sync interval may need adjustment
