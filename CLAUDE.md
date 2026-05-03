# Trackwise — Program Management Tool

**Project**: Trackwise
**Organization**: nileshg502
**Email**: nileshg502@gmail.com
**Repo**: https://github.com/nileshg502/program-management-tool

---

## Repositories

| Repo | Stack | Purpose |
|------|-------|---------|
| `program-management-tool` | Markdown, YAML | Documentation, requirements, ADRs, agile artifacts |

---

## Roles

| Role | Focus | File |
|------|-------|------|
| PM | Requirements, priorities, stakeholder communication | [roles/PM.md](roles/PM.md) |
| BA | Analysis, specifications, data models | [roles/BA.md](roles/BA.md) |
| Architect | Design, ADRs, technical decisions | [roles/ARCHITECT.md](roles/ARCHITECT.md) |
| UX Designer | Wireframes, design system, component specs | [roles/UX-DESIGNER.md](roles/UX-DESIGNER.md) |
| Engineering | Implementation, code, tests | [roles/ENGINEERING.md](roles/ENGINEERING.md) |
| QA | Quality, testing, acceptance criteria | [roles/QA.md](roles/QA.md) |
| DevOps | Infrastructure, CI/CD, deployment | [roles/DEVOPS.md](roles/DEVOPS.md) |
| Forensic | Scope deviation analysis, revenue protection | [roles/FORENSIC.md](roles/FORENSIC.md) |

Activate any role by saying: **"Activate [Role] Role"**

---

## Decision Authority

- **PM** decides what to build (scope, priority). PM decision = Engineering authorization.
- **Architect** decides how to build (ADRs). No PM approval needed for technical decisions.
- **Engineering** decides implementation details. No Architect approval needed per-commit.
- **QA has veto power** on quality — the only mandatory approval gate.
- **Forensic** identifies and presents delta — PM decides Accept / Charge / Remove.

---

## API Contract

**ALL API fields are snake_case — no exceptions.**
See [api-contract/CONVENTIONS.md](api-contract/CONVENTIONS.md) for full rules.

---

## Key Resources

- [SESSION-STATE.md](SESSION-STATE.md) — Current status, environment, next steps
- [requirements/](requirements/) — PRDs, PM process document, use cases
- [architecture/](architecture/) — System design, data model
- [decisions/](decisions/) — ADRs and decision records
- [agile/](agile/) — Stories, sprints, backlogs
- [projects/](projects/) — Per-project folders (baseline, status, sprints, forensic)
- [projects/templates/](projects/templates/) — Templates for onboarding new projects
- [forensic/](forensic/) — Forensic role outputs (baseline, runs, delta documents)
- [baseline/](baseline/) — Repo-level contract baselines
- [change-requests/](change-requests/) — Active and logged change requests

---

## Project Overview

Trackwise is a structured workspace for managing multiple concurrent software delivery projects. It provides a single source of truth for requirements, architecture decisions, sprint planning, scope deviation tracking, and leadership reporting.

- **Go-Live**: TBD
- **Scale**: TBD
- **RBAC Roles**: Admin, PM, BA, Engineer, QA, DevOps, Viewer
- **Project Types**: Fixed Scope / Fixed Budget
- **Forensic Tracking**: Scope deviation and revenue protection via Forensic Role

---

## Session-End Checklist

Before ending any session:
1. Commit and push all changes
2. Update SESSION-STATE.md with what was done
3. Verify `git status` is clean in every repo you touched
