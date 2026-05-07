# Session State

**Last Updated:** 2026-05-07 (Session 6)
**Current Branch:** master
**Total Delivered:** 0 points across 0 sprints

---

## Current State

Session 6 — UX Designer Role: Mockup fully rebuilt. All 14 screens covered, plus 2 new drawers (Manage User S-15, Manage Client S-16), 1 new modal (File Preview), and a multi-file tracker with parsed/raw views. Final visual treatment: Violet → Pink palette on Warm Cream canvas. Palette comparison tool also delivered (`design/mockups/trackwise-palettes.html`) — 4 palettes × 7 backgrounds = 28 combinations.

---

## Changes Made This Session (5)

**1. ADR-001 — Tech Stack Selection — DONE**
- NestJS + TypeScript on AWS Lambda
- API Gateway (HTTP API), EventBridge Scheduler + Lambda for GitHub sync
- React + TypeScript on S3 + CloudFront
- MongoDB Atlas M0 free tier, Mongoose ODM
- GitHub Actions CI/CD

**2. ADR-002 — Database and Data Layer — DONE**
- Single database, org_id on every document (multi-tenant)
- 9 collections defined with full field schemas
- Compound indexes defined — org_id as leading field on all

**3. ADR-003 — Authentication and RBAC — DONE**
- AWS Cognito User Pool — single Manager role (CTO / CEO / PM identical permissions)
- No employee login — PM manages all employee records
- Any Manager creates a new user by entering name, email, password — user immediately active, no invite email
- User can change their own password after login
- org_id stored as Cognito custom attribute — extracted from JWT on every request

**4. ADR-004 — GitHub Integration Strategy — DONE**
- GitHub OAuth App authentication
- One Trackwise project = one GitHub repo
- Reads: milestones, sprint labels, blocked label only
- Configurable sync interval per org (5–60 min, default 15 min)
- Last known data shown on sync failure + error indicator + timestamp
- On-demand sync via "Sync Now" button per project
- File-based report reading (compliance, security, rework) deferred as future enhancement

**5. ADR-005 — Frontend Architecture and Routing — DONE**
- Redux Toolkit with RTK Query for API calls
- Ant Design component library
- React Router v6
- On-demand data refresh only — no background polling on frontend

**6. ADR-006 — Notification and Alert Delivery — DONE**
- In-app notifications only — no email in v1.0
- Sync Lambda evaluates alert conditions after each sync
- 5 alert types with deduplication per type + project
- Alert history per project

**7. ADR-007 — Forensic Data Source — DONE**
- cr-tracker.md read from GitHub repo via Contents API during sync
- Configurable path per project (cr_tracker_path field on project)
- Totals table parsed — 5 values extracted (total, absorbed, converted, pending, removed)
- Graceful handling: not_configured / file_not_found / parse_error states

**8. Architecture Documents Updated — DONE**
- system-overview.md — full system diagram, component table, request flows, NFRs, ADR index
- data-model.md — all 9 collections with full field schemas, indexes, entity relationships

---

## Decisions Made This Session (5)

- Tech stack: NestJS + TypeScript on AWS Lambda, React + TypeScript, MongoDB Atlas M0, Mongoose, GitHub Actions
- Multi-tenancy: single database, org_id on every document, enforced at NestJS service layer
- Auth: AWS Cognito, single Manager role, direct user creation (no invite email)
- GitHub: OAuth App, one repo per project, milestones + sprint labels + blocked label only
- Frontend: Redux Toolkit, Ant Design, React Router v6, on-demand refresh only
- Alerts: in-app only, evaluated by sync Lambda, 5 types with deduplication
- Forensic data: cr-tracker.md read from GitHub repo, configurable path, Totals table parsed

---

## Session History

| Session | Role | Summary |
|---------|------|---------|
| 1 | PM | Project setup, roles defined, initial structure created |
| 2 | PM | Tool integration and multi-project workflow discussed (deferred) |
| 3 | PM + Forensic | Forensic Role redesigned — 5 documents, CR tracker, templates created. Trackwise PRD v1.0 written |
| 4 | BA | 8 Epics and 49 Stories created. Product backlog updated — 209 points total |
| 5 | Architect | 7 ADRs completed and approved. Architecture documents fully updated |
| 6 | UX Designer | Full mockup rebuild — all 14 screens with design tokens, SVG icons, fixed shell, polished components. Added Manage User drawer (S-15), Manage Client drawer (S-16), File Preview modal (parsed + raw markdown), multi-file tracker, palette comparison tool. Final treatment: Violet → Pink + Warm Cream |

---

## Mockup Additions This Session

- **Manage User drawer (S-15)** — Reset password / Change role / Disable / Delete + activity log + Cognito metadata
- **Manage Client drawer (S-16)** — Editable details, KPIs, project list, activity, Archive/Delete
- **File Preview modal** — Parsed view (CR Tracker totals + entries / Compliance checks / Security findings) + Raw markdown view
- **Multi-file tracker** in Project Settings — repeater with file-type select, parse status badges, preview links — flags need to reopen ADR-004 (deferred multi-file reading)
- **Palette comparison tool** at `design/mockups/trackwise-palettes.html` — 4 palettes (Indigo→Mint, Coral→Plum, Violet→Pink, Teal-only) × 7 backgrounds (Smoke white, Pure white, Warm cream, Soft mint, Lavender mist, Slate, Dark mode)
- **Final palette applied to main mockup**: Violet → Pink (#7C5CFC → #EC4899) on Warm Cream (#FAF7F2) — aurora auth background, gradient brand marks, status-aware project card stripes, hero KPI

---

## Pending / Next Steps

- **ADR-004 reopen** — Multi-file reader is now visualised in the mockup but originally deferred to v2. PM/Architect decision needed: ship in v1.0 or hold and mark mockup as forward-looking.
- **EP-01 needs a new story** — User creation flow: any Manager enters name, email, password → Cognito adminCreateUser → user immediately active. PM to write story and add to backlog.
- **EP needs new stories for Manage flows** — S-15 (Manage User: reset pw, change role, disable, delete) and S-16 (Manage Client: edit, archive, delete) are not currently in the backlog.
- Resolve 3 open PRD questions before sprint planning:
  - GitHub label convention for sprints → blocks ST-020, ST-045
  - Exact threshold for At Risk vs Behind → blocks ST-019
  - Partially Available definition — hours or PM flag → blocks ST-026
- CTO review of forensic templates in `projects/templates/forensic/`
- **UX wireframes** — dashboard, project detail view, people directory, notification panel
- **Sprint planning** — architecture complete; EP-01 and EP-08 ready to schedule once open PRD questions resolved
- Future enhancements (PM to specify when ready):
  - File-based report visualisation (compliance, security, rework) — deferred from ADR-004
  - Employee self-service login and domain knowledge editing — deferred from ADR-003
  - Email notifications — deferred from ADR-006
  - Monorepo GitHub support — deferred from ADR-004
- Tool integration discussion — deferred from Session 2, still pending
- Multi-project workflow design — deferred from Session 2, still pending
