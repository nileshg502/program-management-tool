# ST-001 — Set up project repository, tech stack, and CI pipeline

**Story ID:** ST-001
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P0
**Points:** 5
**Status:** Not Started

---

## User Story

As an Engineering team, I need the project repository initialised with the agreed tech stack and a working CI pipeline, so that all subsequent development has a consistent, automated baseline.

---

## Acceptance Criteria

- [ ] Repository created and accessible to all team members
- [ ] Tech stack selected and documented (framework, database, ORM, test runner)
- [ ] Local development setup documented — one command to run locally
- [ ] CI pipeline runs on every pull request: lint, unit tests, build
- [ ] Environment variable management in place — no secrets in repo
- [ ] Staging environment provisioned and accessible
- [ ] README covers setup, run, test, and deploy steps

---

## Notes

- Tech stack decision is an Architect call — Engineering follows ADR once recorded
- CI provider TBD (GitHub Actions assumed unless Architect decides otherwise)
