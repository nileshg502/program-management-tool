# ST-046 — Sync blocked label — count of open issues tagged blocked

**Story ID:** ST-046
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As the system, I need to sync the count of open issues tagged "blocked" per sprint, so that sprint health displays the correct blocked count and alert thresholds can be evaluated.

---

## Acceptance Criteria

- [ ] During sprint label sync (ST-045), blocked issues are identified by the "blocked" label (case-insensitive)
- [ ] `blocked_count` on the Sprint record is updated on every sync
- [ ] The "blocked" label name is configurable per project — defaults to "blocked"
- [ ] `GET /api/v1/projects/:id/sprints/current` includes `blocked_count` in the response
- [ ] Blocked count feeds the alert trigger in ST-037

---

## Notes

- This story is largely implemented as part of ST-045 — listed separately because blocked label is a distinct configurable concern and feeds a separate alert trigger
- Confirm "blocked" as the standard label name with PM before implementation
