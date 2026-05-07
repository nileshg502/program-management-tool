# ST-018 — Milestone health section — list with status per milestone

**Story ID:** ST-018
**Epic:** EP-04 — Project Detail View
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see all milestones for a project with their due dates, % complete, and health status, so that I can track delivery progress at a milestone level.

---

## Acceptance Criteria

- [ ] Milestone section lists all milestones for the project, ordered by due date ascending
- [ ] Each row shows: milestone name, due date, % complete, status badge (On Track / At Risk / Behind)
- [ ] % complete = closed issues / total issues on the milestone × 100, rounded to nearest integer
- [ ] Closed milestones shown with a "Completed" badge and greyed out
- [ ] If no milestones exist in GitHub — section shows "No milestones found in GitHub"
- [ ] If GitHub not yet connected — section shows "GitHub integration not configured"
- [ ] Section data sourced from synced GitHub milestone data (EP-08)

---

## Notes

- Status calculation logic is in ST-019 — this story handles display only
- % complete formula depends on issue counts from EP-08 sync
