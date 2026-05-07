# EP-03 — Client & Project Dashboard

**Epic ID:** EP-03
**Title:** Client and Project Dashboard
**PRD Reference:** Section 7.2
**Priority:** P1
**Status:** Not Started

---

## Goal

Give CTO/CEO a single top-level screen showing all clients, their active projects, and the worst-case health status across each client's portfolio.

---

## Scope

- Client entity — name, industry, active project count
- Project entity — name, code, client, delivery type, go-live date, GitHub repo
- Dashboard view — client cards with project rows
- Status roll-up — worst status across projects surfaces on client card
- Colour coding — Green / Amber / Red
- Navigation — clicking a project opens Project Detail (EP-04)

---

## Out of Scope

- Project detail content (EP-04)
- GitHub data sync (EP-08)
- Milestone status calculation depends on EP-08

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-011 | Create Client entity and admin CRUD | 3 |
| ST-012 | Create Project entity linked to Client | 5 |
| ST-013 | Dashboard — client card list with project rows | 5 |
| ST-014 | Status roll-up logic — worst status across projects per client | 5 |
| ST-015 | Colour-coded status badges — Green / Amber / Red | 2 |
| ST-016 | Navigate from project row to Project Detail view | 2 |

**Total Points:** 22
