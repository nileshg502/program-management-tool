# ST-043 — Store GitHub repo URL on Project entity

**Story ID:** ST-043
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 2
**Status:** Not Started

---

## User Story

As a PM or admin, I need to enter the GitHub repository URL for a project, so that the sync process knows which repo to pull data from.

---

## Acceptance Criteria

- [ ] `github_repo_url` field exists on Project entity (already in ST-012 schema)
- [ ] PM/admin can add or update the GitHub repo URL on the project settings screen
- [ ] URL validated as a valid GitHub repository URL format (`https://github.com/{owner}/{repo}`)
- [ ] Invalid URL format rejected with validation message
- [ ] If no URL is set — GitHub-dependent sections show "GitHub repo not configured"
- [ ] `github_repo_url` included in `GET /api/v1/projects/:id` response

---

## Notes

- This story ensures the URL field is surfaced in the UI — the schema field itself is part of ST-012
