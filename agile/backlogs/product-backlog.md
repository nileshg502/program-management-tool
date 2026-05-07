# Product Backlog

**Last Updated:** 2026-05-05
**Total Points:** 209
**Total Stories:** 49
**Total Epics:** 8

---

## Epics Summary

| Epic | Title | Stories | Points | Priority |
|------|-------|---------|--------|----------|
| EP-01 | Foundation & Authentication | 5 | 23 | P0 |
| EP-02 | People Directory | 5 | 23 | P1 |
| EP-03 | Client & Project Dashboard | 6 | 22 | P1 |
| EP-04 | Project Detail View | 6 | 27 | P1 |
| EP-05 | Team Assignment & Availability | 6 | 24 | P2 |
| EP-06 | Forensic Summary Integration | 5 | 16 | P2 |
| EP-07 | Alerts & Notifications | 8 | 32 | P2 |
| EP-08 | GitHub Integration | 8 | 42 | P1 |

---

## Prioritized Backlog

### P0 — Must ship first (Foundation)

| ID | Title | Epic | Points | Status | Dependencies |
|----|-------|------|--------|--------|--------------|
| ST-001 | Set up project repository, tech stack, and CI pipeline | EP-01 | 5 | Not Started | — |
| ST-002 | Create Organisation entity and seed data | EP-01 | 3 | Not Started | ST-001 |
| ST-003 | User registration and login | EP-01 | 5 | Not Started | ST-001 |
| ST-004 | Implement RBAC — CTO/CEO and PM roles | EP-01 | 5 | Not Started | ST-003 |
| ST-005 | Route and API permission guards by role | EP-01 | 5 | Not Started | ST-004 |

**EP-01 Total: 23 points**

---

### P1 — Core features

| ID | Title | Epic | Points | Status | Dependencies |
|----|-------|------|--------|--------|--------------|
| ST-006 | Create Employee entity and admin CRUD | EP-02 | 5 | Not Started | ST-001 |
| ST-007 | Create Domain Knowledge entity linked to Employee | EP-02 | 3 | Not Started | ST-006 |
| ST-008 | Employee self-service — add and edit own domain knowledge | EP-02 | 5 | Not Started | ST-007 |
| ST-009 | People directory list view for management | EP-02 | 5 | Not Started | ST-007 |
| ST-010 | Search and filter directory by role, domain, availability | EP-02 | 5 | Not Started | ST-009 |
| ST-011 | Create Client entity and admin CRUD | EP-03 | 3 | Not Started | ST-001 |
| ST-012 | Create Project entity linked to Client | EP-03 | 5 | Not Started | ST-011 |
| ST-013 | Dashboard — client card list with project rows | EP-03 | 5 | Not Started | ST-012 |
| ST-014 | Status roll-up logic — worst status across projects per client | EP-03 | 5 | Not Started | ST-013, ST-019 |
| ST-015 | Colour-coded status badges — Green / Amber / Red | EP-03 | 2 | Not Started | — |
| ST-016 | Navigate from project row to Project Detail view | EP-03 | 2 | Not Started | ST-013 |
| ST-017 | Project overview section — static fields display | EP-04 | 3 | Not Started | ST-012 |
| ST-018 | Milestone health section — list with status per milestone | EP-04 | 5 | Not Started | ST-044 |
| ST-019 | Milestone status calculation — On Track / At Risk / Behind | EP-04 | 8 | Not Started | ST-044 |
| ST-020 | Sprint health section — committed vs completed vs blocked | EP-04 | 5 | Not Started | ST-045 |
| ST-021 | Team section — assigned people with role and domain | EP-04 | 3 | Not Started | ST-023 |
| ST-022 | Forensic summary section — CR totals by decision type | EP-04 | 3 | Not Started | ST-029 |
| ST-042 | GitHub OAuth / PAT authentication per organisation | EP-08 | 8 | Not Started | ST-001 |
| ST-043 | Store GitHub repo URL on Project entity | EP-08 | 2 | Not Started | ST-012 |
| ST-044 | Sync GitHub milestones — name, due date, issue counts | EP-08 | 8 | Not Started | ST-042, ST-043 |
| ST-045 | Sync sprint label issues — open vs closed per sprint label | EP-08 | 8 | Not Started | ST-042, ST-043 |
| ST-046 | Sync blocked label — count of open issues tagged blocked | EP-08 | 5 | Not Started | ST-045 |
| ST-047 | Scheduled auto-sync every 15 minutes | EP-08 | 5 | Not Started | ST-044, ST-045 |
| ST-048 | Manual refresh trigger per project | EP-08 | 3 | Not Started | ST-047 |
| ST-049 | Sync failure warning displayed on project | EP-08 | 3 | Not Started | ST-047 |

**EP-02 Total: 23 points | EP-03 Total: 22 points | EP-04 Total: 27 points | EP-08 Total: 42 points**

---

### P2 — Secondary features

| ID | Title | Epic | Points | Status | Dependencies |
|----|-------|------|--------|--------|--------------|
| ST-023 | Assign and remove employee from a project | EP-05 | 5 | Not Started | ST-006, ST-012 |
| ST-024 | Show team composition — role and domain per member | EP-05 | 3 | Not Started | ST-023 |
| ST-025 | Domain knowledge mismatch flag on project | EP-05 | 5 | Not Started | ST-023, ST-007 |
| ST-026 | PM sets Partially Available flag on an assignment | EP-05 | 3 | Not Started | ST-023 |
| ST-027 | Organisation-wide availability and bench view | EP-05 | 5 | Not Started | ST-023 |
| ST-028 | Filter availability view by domain and status | EP-05 | 3 | Not Started | ST-027 |
| ST-029 | Create CR Summary entity linked to Project | EP-06 | 3 | Not Started | ST-012 |
| ST-030 | Manual CR Summary entry and update by PM | EP-06 | 5 | Not Started | ST-029 |
| ST-031 | Forensic one-line indicator on project card | EP-06 | 3 | Not Started | ST-029 |
| ST-032 | "Forensic tracking not initiated" state handling | EP-06 | 2 | Not Started | ST-029 |
| ST-033 | Forensic summary display on project detail view | EP-06 | 3 | Not Started | ST-029, ST-030 |
| ST-034 | Create Alert entity and notification store | EP-07 | 3 | Not Started | ST-001 |
| ST-035 | Alert trigger — milestone status change to Behind | EP-07 | 5 | Not Started | ST-034, ST-019 |
| ST-036 | Alert trigger — milestone status change to At Risk | EP-07 | 3 | Not Started | ST-034, ST-019 |
| ST-037 | Alert trigger — sprint blocked stories exceed 3 | EP-07 | 5 | Not Started | ST-034, ST-046 |
| ST-038 | Alert trigger — forensic CR Pending for more than 1 sprint | EP-07 | 5 | Not Started | ST-034, ST-029 |
| ST-039 | Alert trigger — team domain knowledge mismatch on project | EP-07 | 3 | Not Started | ST-034, ST-025 |
| ST-040 | In-app notification panel with unread count badge | EP-07 | 5 | Not Started | ST-034 |
| ST-041 | Alert history view per project | EP-07 | 3 | Not Started | ST-034 |

**EP-05 Total: 24 points | EP-06 Total: 16 points | EP-07 Total: 32 points**

---

## Backlog Grooming Notes

- ST-008 raises an open question: does "employee self-service" require a third system role (`employee`)? Confirm with PM before sprint planning
- ST-019 and ST-020 are blocked on PRD Open Questions #1 and #2 (sprint label convention, At Risk threshold) — these questions must be resolved before these stories enter a sprint
- ST-026 is blocked on PRD Open Question #3 (Partially Available definition)
- ST-042 (GitHub auth) is the gate for all EP-08 stories — schedule it early in the P1 sprint sequence
- Stories >13 points must be split — all stories in this backlog are ≤13 points
