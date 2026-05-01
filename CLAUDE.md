# Program Management Tool — Spec Repository

**Project**: Program Management Tool
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

---

## Decision Authority

- **PM** decides what to build (scope, priority). PM decision = Engineering authorization.
- **Architect** decides how to build (ADRs). No PM approval needed for technical decisions.
- **Engineering** decides implementation details. No Architect approval needed per-commit.
- **QA has veto power** on quality — the only mandatory approval gate.

---

## API Contract

**ALL API fields are snake_case — no exceptions.**
See [api-contract/CONVENTIONS.md](api-contract/CONVENTIONS.md) for full rules.

---

## Key Resources

- [SESSION-STATE.md](SESSION-STATE.md) — Current status, environment, next steps
- [requirements/](requirements/) — PRDs and requirements
- [architecture/](architecture/) — System design, data model
- [decisions/](decisions/) — ADRs and decision records
- [agile/](agile/) — Stories, sprints, backlogs

---

## Project Overview

Program Management Tool is a structured workspace for managing programs, projects, and cross-functional delivery using agile practices. It provides a single source of truth for requirements, architecture decisions, sprint planning, and team collaboration.

- **Go-Live**: TBD
- **Scale**: TBD
- **RBAC Roles**: Admin, PM, BA, Engineer, QA, DevOps, Viewer

---

## Session-End Checklist

Before ending any session:
1. Commit and push all changes
2. Update SESSION-STATE.md with what was done
3. Verify `git status` is clean in every repo you touched
