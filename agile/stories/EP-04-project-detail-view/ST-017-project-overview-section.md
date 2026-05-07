# ST-017 — Project overview section — static fields display

**Story ID:** ST-017
**Epic:** EP-04 — Project Detail View
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM or CTO/CEO, I need to see the project's key details at the top of the Project Detail page, so that I immediately know which client, project, and delivery context I am looking at.

---

## Acceptance Criteria

- [ ] Overview section displays: client name, project name, project code, delivery type, go-live date, current milestone name
- [ ] Client name links back to the dashboard filtered to that client
- [ ] Delivery type displayed as a human-readable label: "Fixed Scope" or "Fixed Budget"
- [ ] Go-live date formatted as a readable date — not a raw ISO string
- [ ] Current milestone is the earliest open milestone from GitHub data — or "No milestone data" if not yet synced
- [ ] Section is read-only — no inline editing

---

## Notes

- Current milestone relies on EP-08 sync; show placeholder until data is available
