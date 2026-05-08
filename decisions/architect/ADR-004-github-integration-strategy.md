# ADR-004 — GitHub Integration Strategy

**Date:** 2026-05-06
**Status:** Approved
**Decider:** Architect + PM

---

## Context

Trackwise reads delivery data from GitHub to populate milestone health, sprint health, and blocked story counts on the dashboard and project detail view. The PRD defines GitHub as the source of truth for delivery status at milestone and sprint level only. This ADR defines how Trackwise connects to GitHub, what it reads, and how sync is managed.

---

## Decision

### GitHub Authentication
GitHub OAuth App — one OAuth App registered for the Trackwise deployment.

- PM (or any Manager) connects a GitHub account when adding a project via GitHub OAuth flow
- Trackwise stores the OAuth access token encrypted in MongoDB against the project
- Token is used for all GitHub API calls for that project
- Token refresh handled via GitHub OAuth refresh flow

### Repo Mapping
One Trackwise project maps to one GitHub repository. PM selects the GitHub org and repo from a dropdown (populated via GitHub API after OAuth) when creating or editing a project.

### What Trackwise Reads from GitHub

| Data | GitHub API | Used For |
|------|-----------|---------|
| Milestone name | Milestones API | Project detail — milestone list |
| Milestone due date | Milestones API | Milestone health calculation |
| Milestone open issue count | Milestones API | % complete calculation |
| Milestone closed issue count | Milestones API | % complete calculation |
| Issues with sprint label | Issues API — filter by label | Sprint stories committed vs complete |
| Issues with sprint label — closed | Issues API — filter by label + state:closed | Sprint stories complete count |
| Issues with "blocked" label | Issues API — filter by label | Blocked story count |

**What Trackwise does NOT read:**
- Individual commits
- Pull requests
- Code content
- Comments
- File contents (deferred to future enhancement)

### Sprint Label Convention
PM configures the sprint label prefix per project at project creation (e.g. `sprint-1`, `sprint-2` or `Sprint 1`, `Sprint 2`). Trackwise derives the current sprint label from the configured prefix + current sprint number stored in MongoDB.

### Sync Schedule
- Default sync interval: 15 minutes
- Configurable per organisation — stored in `organisations.github_sync_interval_minutes`
- Valid range: 5–60 minutes
- Sync is triggered by EventBridge Scheduler invoking the sync Lambda per org

### Sync Failure Handling
- On sync failure — last known data is displayed on the dashboard
- A "Last synced at [timestamp]" indicator shown on every project card and project detail view
- If last sync failed — an error indicator shown alongside the timestamp (e.g. "Sync failed — showing data from [timestamp]")
- Sync failures logged to CloudWatch — Dead Letter Queue captures failed Lambda invocations

### On-Demand Sync
- Manual "Sync now" button available on the project detail view
- Triggers the sync Lambda immediately for that project only
- UI shows a loading state during sync and refreshes data on completion
- Available to any Manager

---

## Rationale

### GitHub OAuth App over Personal Access Token (PAT)
- PAT is per-user and expires — requires manual rotation, breaks silently
- OAuth App issues tokens scoped to the connected account — standard, revocable, auditable
- PM can connect and disconnect GitHub access from within Trackwise without developer intervention

### One repo per project
- Clean 1:1 mapping — simplest to implement and reason about
- Covers the majority of delivery setups where each client project has its own repo
- Monorepo support (multiple projects in one repo) deferred to future enhancement

### Configurable sync interval stored per org
- PRD requirement — polling must be configurable
- Stored at org level so all projects in an org share the same interval
- EventBridge Scheduler rule updated via API when org settings change

### Last known data on sync failure
- Dashboard must always show something — blank screens or error states reduce trust
- Timestamp tells the user exactly how stale the data is
- Error indicator signals the problem without hiding data

### On-demand sync
- Gives PM control without waiting for the next scheduled sync
- Critical after a sprint milestone is updated in GitHub and PM needs the dashboard to reflect it immediately

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Personal Access Token (PAT) | Expires silently, manual rotation, no audit trail — OAuth App is more robust |
| GitHub App (org installation) | More setup complexity, requires GitHub org admin access — OAuth App is sufficient for v1.0 |
| Webhook-based sync (push from GitHub) | Requires a public endpoint, webhook management per repo, retry logic — polling is simpler and sufficient at this scale |
| Read repo files for reports | Deferred — format, schema, and tooling not yet defined. Logged as future enhancement for PM to specify |

---

## Future Enhancements (Deferred)

The following were discussed and explicitly deferred to a future PRD update:

| Enhancement | Notes |
|-------------|-------|
| File-based report visualisation | Read compliance, security, rework reports from repo files and display on dashboard. PM to specify: which reports, what format, what generates them, v1.1 or new Epic |
| Monorepo support | Multiple Trackwise projects mapped to subfolders within one GitHub repo |
| Branch selection per project | Track a specific branch rather than default branch |
| Workload visibility from GitHub assignees | Who is working on what — requires assignee data + definition of "active" |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| GitHub API rate limit — 5,000 requests/hour per OAuth token | At 15-min intervals reading 3 API endpoints per project, rate limit is not a concern at 5–8 orgs | Org count exceeds 50 or sync interval drops below 5 min |
| Sprint label convention varies per team | PM configures label prefix per project at setup | Standardise label convention across org |
| OAuth token tied to the connecting user's GitHub account | If that user leaves, token may be revoked — PM must reconnect | User offboarding process |

---

## Consequences

- `projects` collection must store: `github_repo`, `github_org`, `github_oauth_token` (encrypted), `github_sprint_label_prefix`, `last_synced_at`, `last_sync_status`
- Sync Lambda must update `last_synced_at` and `last_sync_status` on every run — success or failure
- EventBridge Scheduler rule must be created/updated when org sync interval changes
- GitHub OAuth App client ID and secret stored in AWS Secrets Manager — not in environment variables
- Frontend must show "Last synced at" and sync error state on every project card

---

## Review Trigger

Revisit this ADR when:
- File-based report reading is specified by PM
- GitHub rate limits are approached
- Monorepo support is requested
- OAuth token management becomes a support burden

---

## Amendment 2026-05-08 — Sync model narrowed by ADR-008

This ADR's original scheduled-sync model (EventBridge → sync Lambda → mirror tables every 15 min) is **superseded for the dashboard read path** by [ADR-008](ADR-008-data-ownership-and-sync.md):

- The dashboard's GitHub-mirrored fields (milestones + sprint membership) are served via **server-side, on-demand cache** with 60-second TTL — not via scheduled mirror tables
- File reading is **rejected** (PM Session 7) — the "deferred to future enhancement" line on file contents is now a hard "out of scope," not "deferred"
- The `projects` schema fields originally proposed by this ADR (`last_synced_at`, `last_sync_status`) are dropped — there is no scheduled sync to track
- Encrypted OAuth token storage on `projects` (`github_oauth_token`) **is retained** — backend cache calls still authenticate to GitHub using this token
- `github_sprint_label_prefix` **is retained** but renamed to `sprint_label_pattern` per PRD §12.1, defaulting to `sprint-N`

**No further changes to this ADR's intent are required.** The OAuth strategy, repo mapping, and "what we read from GitHub" tables remain authoritative. The mechanism (cache vs mirror) is in ADR-011.
