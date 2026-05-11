# Product Backlog

**Last Updated:** 2026-05-11
**Total Points:** 273
**Total Stories:** 63
**Total Epics:** 10

---

## Epics Summary

| Epic | Title | Stories | Points | Priority |
|------|-------|---------|--------|----------|
| EP-01 | Foundation & Authentication | 10 | 39 | P0 |
| EP-02 | People Directory | 5 | 23 | P1 |
| EP-03 | Client & Project Dashboard | 9 | 33 | P1 |
| EP-04 | Project Detail View | 6 | 27 | P1 |
| EP-05 | Team Assignment & Availability | 6 | 24 | P2 |
| EP-06 | Forensic Summary Integration | 5 | 16 | P2 |
| EP-07 | Alerts & Notifications | 8 | 32 | P2 |
| EP-08 | GitHub Integration | 8 | 42 | P1 |
| EP-09 | Project Health Dashboard | 3 | 19 | P1 |
| EP-10 | Sprint Health | 3 | 18 | P1 |

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
| ST-050 | Create user flow — direct Cognito creation by Manager | EP-01 | 5 | Not Started | ST-003, ST-004 |

**EP-01 Total: 28 points (P0)**

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
| ST-055 | Manage Client — edit client details | EP-03 | 3 | Not Started | ST-011 |
| ST-056 | Manage Client — archive client | EP-03 | 3 | Not Started | ST-011, ST-012 |
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
| ST-058 | CR Summary — add origin classification and source deviation link | EP-09 | 3 | Not Started | ST-029, ST-030 |
| ST-059 | Project Health read model and `GET /projects/{id}/health` API | EP-09 | 8 | Not Started | ST-019, ST-020, ST-058 |
| ST-060 | Project Health UI — single-page cockpit screen | EP-09 | 8 | Not Started | ST-059 |
| ST-061 | Sprint Health read model and `GET /projects/{id}/sprint-health` API | EP-10 | 8 | Not Started | ST-044, ST-045 |
| ST-062 | Sprint Health UI — per-sprint view with sprint selector dropdown | EP-10 | 5 | Not Started | ST-061 |
| ST-063 | Sprint Health UI — consolidated `Overall` view across sprints | EP-10 | 5 | Not Started | ST-061, ST-062 |

**EP-02 Total: 23 points | EP-03 Total: 28 points (P1) | EP-04 Total: 27 points | EP-08 Total: 42 points (P1) | EP-09 Total: 19 points (P1) | EP-10 Total: 18 points (P1)**

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
| ST-051 | Manage User — reset password (admin-initiated) | EP-01 | 3 | Not Started | ST-050 |
| ST-052 | Manage User — change user role | EP-01 | 2 | Not Started | ST-050 |
| ST-053 | Manage User — disable and re-enable user | EP-01 | 3 | Not Started | ST-050 |
| ST-054 | Manage User — delete user (hard delete) | EP-01 | 3 | Not Started | ST-050 |
| ST-057 | Manage Client — delete client (cascade hard delete) | EP-03 | 5 | Not Started | ST-056 |

**EP-01 Total: 11 points (P2) | EP-03 Total: 5 points (P2) | EP-05 Total: 24 points | EP-06 Total: 16 points | EP-07 Total: 32 points**

---

## Backlog Grooming Notes

- ST-008 raises an open question: does "employee self-service" require a third system role (`employee`)? Confirm with PM before sprint planning
- ST-019 and ST-020 are blocked on PRD Open Questions #1 and #2 (sprint label convention, At Risk threshold) — these questions must be resolved before these stories enter a sprint
- ST-026 is blocked on PRD Open Question #3 (Partially Available definition)
- ST-042 (GitHub auth) is the gate for all EP-08 stories — schedule it early in the P1 sprint sequence
- ST-050 supersedes ST-003's "registration" path — when ST-050 is scheduled, re-scope ST-003 to "login + logout only" so the two stories don't overlap
- ST-051, ST-053, ST-054 — PM approved on 2026-05-07 to bring reset-password, disable/re-enable, and delete in-app for v1.0. ADR-003 must be amended to match (Architect action). Stories are unblocked for sprint planning; the ADR amendment is a documentation follow-up, not a gate
- ST-055 makes ST-011's "deactivate" action redundant — re-scope ST-011 when ST-055 is scheduled so deactivate is replaced by archive (ST-056) semantics
- **GitHub file reading rejected by PM on 2026-05-07.** Trackwise reads only milestones, sprint labels, and blocked labels from GitHub (per ADR-004). All forensic, compliance, and security data is stored in Trackwise's own database and exposed via Trackwise APIs. Consequences:
  - ADR-004 stays as-is — no reopen needed
  - ADR-007 must be updated by Architect — current ADR-007 says CR data is parsed from `cr-tracker.md` in the repo; new direction is "CR data lives in Trackwise database, no markdown source"
  - Mockup multi-file tracker + File Preview modal must be removed by UX in the next mockup pass
  - ST-058–ST-061 (the four File Preview stories drafted earlier this session) have been deleted from the backlog
  - ST-029 (CR Summary entity) and ST-030 (manual CR entry by PM) become *the* path for CR data, not a fallback
- Stories >13 points must be split — all stories in this backlog are ≤13 points
- **EP-09 (Project Health Dashboard) added 2026-05-08 by PM.** Single-view cockpit per project covering sprint, milestone, QA, CR-by-origin, and deviation data. ST-058 adds CR origin classification (data); ST-059 adds the read API; ST-060 adds the UI. ST-059 is gated on PRD Open Questions #1 and #2 (sprint label convention, At Risk threshold) — must be resolved before ST-059 enters a sprint
- **Data ownership rule confirmed by PM on 2026-05-08.** All Trackwise UI data must be stored in the Trackwise database. Frontend reads only Trackwise APIs — never GitHub or other external systems directly. Backend may call GitHub server-side with a 60-second TTL cache for milestone metadata and sprint membership only. No scheduled sync, no mirror table, no "last synced" UX, no manual refresh button for v1.0. ADR-008 (drafted, awaiting Architect review) codifies this; ADR-007 will be marked Superseded
- **PRD Open Questions #1 and #2 resolved by PM on 2026-05-08.** Q1 — sprint label pattern is `sprint-N` (numeric suffix); `sprint_label_pattern` field on Project defaults to `sprint-N` per ST-045; current sprint is derived, no `current_sprint_number` field needed. Q2 — At Risk / Behind status computed at read time using burn-rate; bands are 10% / 25% on issue-count basis; full rules in PRD §12.2. ST-019, ST-020, ST-045, ST-059 are unblocked
- **EP-09 Deviation and QA dependencies parked by PM on 2026-05-08.** EP-09's dashboard reads QA Reports and Deviations, neither of which has a backlog entity yet. PM has parked the schema decision — either PM will define the structure, or PM will provide an existing DB and we build against it. Until resolved, ST-059 stubs the affected sections (returns `data_available: false` with reason `"awaiting schema"`). ST-058's `source_deviation_id` is a soft reference for v1.0 — strict foreign-key validation deferred until the Deviation entity ships
- **EP-10 (Sprint Health) added 2026-05-11 by PM.** Per-project sprint health view with sprint selector dropdown (`Overall` default + every sprint label). Four blocks per sprint: Epics & Stories, Planned vs Completed, Bugs Logged vs Resolved, End-of-sprint Carryover. ST-061 adds the API; ST-062 adds the per-sprint UI; ST-063 adds the consolidated `Overall` trend table. v1.0 is count-based only — hours / charts / drill-downs deferred. Sits underneath EP-09's Sprint Progress tile, not a replacement. Sprint window date rule (label creation vs milestone due-date) to be locked at ST-061 grooming. Reuses ADR-011 cache — no new DB collections
