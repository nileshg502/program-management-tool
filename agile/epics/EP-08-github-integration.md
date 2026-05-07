# EP-08 — GitHub Integration

**Epic ID:** EP-08
**Title:** GitHub Integration
**PRD Reference:** Section 8
**Priority:** P1
**Status:** Not Started

---

## Goal

Connect each project to its GitHub repository and sync milestone and sprint data automatically every 15 minutes. Manual refresh must also be available. This Epic is a dependency for milestone status calculation (EP-04 ST-019) and sprint health display (EP-04 ST-020).

---

## Scope

**What is synced:**
- Milestones — name, due date, open vs closed issue count
- Sprint labels — stories tagged with sprint label, open vs closed status
- Blocked label — issues tagged "blocked"

**What is NOT synced:**
- Individual commits
- Pull requests
- Code content
- Comments

**Behaviour:**
- GitHub repo URL stored on Project entity
- OAuth or PAT-based GitHub auth per organisation
- Sync runs every 15 minutes automatically
- Manual refresh available per project
- Sync failure surfaced as a project-level warning

---

## Out of Scope

- Integration with any tool other than GitHub (v1.0)
- Commit-level or PR-level data

---

## Open Questions (from PRD)

| # | Question | Owner | Status |
|---|----------|-------|--------|
| 1 | Which GitHub label convention is used for sprints? | PM | Open |

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-042 | GitHub OAuth / PAT authentication per organisation | 8 |
| ST-043 | Store GitHub repo URL on Project entity | 2 |
| ST-044 | Sync GitHub milestones — name, due date, issue counts | 8 |
| ST-045 | Sync sprint label issues — open vs closed per sprint label | 8 |
| ST-046 | Sync blocked label — count of open issues tagged blocked | 5 |
| ST-047 | Scheduled auto-sync every 15 minutes | 5 |
| ST-048 | Manual refresh trigger per project | 3 |
| ST-049 | Sync failure warning displayed on project | 3 |

**Total Points:** 42
