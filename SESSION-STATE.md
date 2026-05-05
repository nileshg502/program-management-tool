# Session State

**Last Updated:** 2026-05-05 (Session 3)
**Current Branch:** master
**Total Delivered:** 0 points across 0 sprints

---

## Current State

Session 3 — Forensic Role Enhancement & Trackwise PRD: Redesigned the Forensic Role with an agreed document set, templates, and a new existing project flow. Created the Trackwise PRD v1.0 covering the core management tool vision.

---

### Changes Made This Session (3)

**1. Forensic Role — Major Redesign — DONE**

Document set reduced from open-ended to 5 agreed documents:
- `contract-baseline.md` — locked at contract signing
- `sprint-baseline-S[n].md` — locked every sprint before dev starts
- `CR-[ProjectCode]-[Sn]-[Seq].md` — one per deviation found
- `cr-tracker.md` — consolidated view of all CRs and totals
- `REM-[ProjectCode]-[Sn]-[Seq].md` — removal instructions for Engineering

Key changes to the role:
- Drift log dropped — CR tracker covers all tracking needs
- Last-run.md dropped — derivable from CR tracker
- Run report dropped — no separate run report needed
- Four-tag system replaced with Deviation Type (Exceeding / Missing / Different) + PM Decision (Absorb / Convert / Remove / Pending)
- Run 3 and Run 4 now repeat every sprint with sprint suffix (Run-3-S1, Run-4-S1 etc)
- Mandatory traceability block on every deviation — Epic → Story → AC → Run → Layer
- CR log includes full AC section — Original AC, What Was Found, Deviation Type
- Removal task template includes dependencies, preservation list, verification checklist
- Check 0 added — Epic/Story lineage check runs before any baseline check
- No story parent = drift, automatically

**2. Forensic Templates — DONE**
Five individual template files created in `projects/templates/forensic/`:
- `contract-baseline.md`
- `sprint-baseline.md`
- `cr-log.md`
- `cr-tracker.md`
- `removal-task.md`

**3. Initial Baseline Forensic — Existing Projects — DONE**
New 6-phase flow added to the Forensic Role for projects already in delivery:
- Phase 1: Baseline establishment from SOW and documents
- Phase 2: PM provides codebase root path once — role navigates itself
- Phase 3: Epic/Story sweep — two checks per story (AC vs baseline, code vs AC)
- Phase 4: Sweep summary presented before any CR logs generated
- Phase 5: CR logs created, PM assigns decisions, cr-tracker updated
- Phase 6: Forward tracking on standard Run 3/4/5/6 cycle

**4. Pros and Cons of Forensic Role — Discussed**
Wide level assessment completed. Key gaps identified:
- Role is entirely manual — no automated trigger if no one activates it
- Cannot catch verbal scope changes
- No time tracking — estimated vs actual hours gap invisible
- No escalation for aging Pending CRs
- Depends on story quality — weak ACs = weak deviation detection
- No portfolio view across projects
- CR tracker has no aging indicator

**5. Trackwise PRD v1.0 — DONE**
Created `requirements/trackwise-prd.md` covering:
- Organisation people directory with domain knowledge (self-declared)
- Client and project dashboard for CTO/CEO
- Project detail view — milestone health, sprint health, team, forensic summary
- GitHub integration at milestone and sprint level only
- Availability and bench view
- Alerts and notifications — 5 trigger types
- RBAC for CTO/CEO and PM
- High level data model — 9 entities
- 3 open questions pending decisions

---

## Decisions Made This Session

- Drift log dropped — CR tracker is the single source of truth
- Run 3 and Run 4 repeat every sprint — not one-time runs
- CR ID format: `CR-[ProjectCode]-[Sn]-[Seq]` — sprint included in ID
- Notional CR Value removed from CR log — management discusses money outside the system
- Source field removed from CR log — internal classification only
- Removal task is a separate document — Engineering needs precise instructions to avoid regression
- Existing project flow uses Epic/Story sweep, not sprint-by-sprint sweep
- Role reads code directly — no Engineering declaration needed
- GitHub integration at milestone and sprint level only — no commits or PRs
- Domain knowledge is self-declared by employees
- PRD will be updated as requirements evolve — version history maintained

---

## Pending / Next Steps

- CTO review of forensic templates in `projects/templates/forensic/`
- Resolve 3 open questions in PRD:
  - GitHub label convention for sprints
  - Exact threshold definition for At Risk vs Behind
  - Partially Available definition — hours or PM flag
- Architecture discussion for Trackwise tool
- BA specifications and data model detail
- UX wireframes for dashboard and project detail view
- Tool integration discussion — deferred from Session 2, still pending
- Multi-project workflow design — deferred from Session 2, still pending
