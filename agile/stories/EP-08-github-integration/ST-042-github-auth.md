# ST-042 — GitHub OAuth / PAT authentication per organisation

**Story ID:** ST-042
**Epic:** EP-08 — GitHub Integration
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As an admin, I need to connect Trackwise to the organisation's GitHub account, so that project data can be synced from GitHub repositories.

---

## Acceptance Criteria

- [ ] Admin can configure GitHub integration via a settings screen
- [ ] Two auth methods supported: GitHub OAuth App or Personal Access Token (PAT)
- [ ] PAT method: admin pastes a PAT — stored encrypted at rest, never returned in API responses
- [ ] OAuth method: admin initiates OAuth flow — token stored encrypted on completion
- [ ] Connection status shown: Connected / Not Connected / Error
- [ ] Admin can revoke the connection — stored token deleted
- [ ] If connection fails or token is invalid — all projects show sync error state
- [ ] `GET /api/v1/github/status` returns connection status (connected, not_connected, error)

---

## Notes

- PAT must have `repo` scope (read-only) — document required scopes in setup guide
- GitHub token is an organisation-level setting — not per-project
- Encryption key management is an Architect decision — coordinate before implementing
