# ST-032 — "Forensic tracking not initiated" state handling

**Story ID:** ST-032
**Epic:** EP-06 — Forensic Summary Integration
**Priority:** P2
**Points:** 2
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to see a clear message when forensic tracking has not been started for a project, so that I know the absence of data is intentional, not a system error.

---

## Acceptance Criteria

- [ ] When no CR Summary record exists for a project — all forensic display locations show "Forensic tracking not initiated"
- [ ] Message appears in: project card indicator (ST-031), project detail forensic section (ST-033)
- [ ] Message is styled in neutral grey — not a warning or error colour
- [ ] Message does not appear as an alert or notification
- [ ] PM sees an "Initiate forensic tracking" prompt/button next to the message — clicking opens the CR Summary form (ST-030)
- [ ] CTO/CEO sees the message but no action button

---

## Notes

- This state is permanent until the PM manually creates the first CR Summary entry
