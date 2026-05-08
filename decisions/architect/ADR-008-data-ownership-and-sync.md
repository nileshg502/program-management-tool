# ADR-008 — Trackwise Data Ownership and Sync Model

**Date:** 2026-05-08
**Status:** Approved
**Decider:** Architect + PM
**Architect Sign-off:** 2026-05-08 — cache strategy, TTL, and ownership boundary validated. Implementation guidance for the cache is detailed in ADR-011.

---

## Context

As of Session 7 (2026-05-07), PM rejected the earlier direction of reading forensic, compliance, and security data from files committed to GitHub repos. The new direction — confirmed in Session 8 (2026-05-08) — is that **everything Trackwise displays must be stored in Trackwise's own database**. The frontend must not call GitHub or any other external system. The backend may call GitHub for a narrow set of mirrored entities (milestones and sprint membership) on a server-side, short-TTL basis.

This ADR codifies the data ownership boundary, the sync/cache model, and the rules every new entity must follow. It supersedes the parts of ADR-007 that described `cr-tracker.md` parsing and complements ADR-004 (GitHub integration).

---

## Decision

### 1. Data ownership model — three categories

| Category | Definition | Examples |
|----------|------------|----------|
| **Trackwise-owned (CRUD)** | Trackwise is the system of record. Created and edited inside Trackwise. Persisted in MongoDB. | Users, Clients, Projects, CR Summaries, Deviations, QA Reports, Sprint health annotations, PM notes, Domain Knowledge, Assignments |
| **GitHub-mirrored (read-only)** | GitHub is the system of record. Trackwise reads server-side and surfaces in the UI but never writes back. | Milestone metadata, sprint label membership, blocked label counts |
| **External / not stored** | Not displayed in Trackwise. | Raw repo files, individual GitHub issues outside the mirrored fields above |

### 2. Frontend data access rule

The frontend reads **only** from Trackwise APIs. It is forbidden to call GitHub, Cognito (beyond auth flows defined in ADR-003), or any other external service for display data. Code review must enforce this.

### 3. Backend GitHub access — short-TTL cache model

For GitHub-mirrored entities, the backend uses an **on-demand, server-side, short-TTL cache** rather than a scheduled mirror table.

- **TTL:** 60 seconds per (project, entity-type) cache key
- **Trigger:** lazy — populated on first request after expiry
- **No scheduled sync job, no mirror table, no webhooks** for v1.0
- **Failure mode:** if GitHub is unreachable and no cache exists, the API returns the rest of the payload with `data_available: false` and a `reason` for the GitHub-sourced section; the call does not fail outright

### 4. No "last synced" UX

Because the cache TTL is 60 seconds and the user has no manual refresh control, "last synced" timestamps are not displayed. Users see fresh-enough data or a graceful unavailability message; they never need to reason about staleness.

### 5. CR data is database-native (supersedes ADR-007)

CR Summary records are written and edited inside Trackwise (per ST-029, ST-030, ST-058). Trackwise no longer parses `cr-tracker.md` from any repo. The `cr_tracker_path` field on the project entity is removed. The `sync_status` field on `cr_summaries` is removed (no sync, no status to track).

---

## Rationale

### Why CRUD-owned over file-mirrored
- Single source of truth — no diff between database and a markdown file
- Forensic, compliance, and security data needs structured fields (origin, decision, hours, links) — markdown tables are fragile
- Users edit through forms, not commits — review and audit trails work properly

### Why short-TTL cache over scheduled mirror
- Only two entity types are GitHub-mirrored — not enough volume to justify a sync job, queue, or mirror schema
- 60s TTL is below the threshold a PM would notice between dashboard loads
- No mirror table means no "what happens when GitHub state diverges from our copy" class of bugs
- Failure mode is graceful — partial payload with a clear inline message

### Why no manual refresh button
- 60s cache makes it unnecessary in normal use
- Adds a button that needs UX, accessibility, rate-limit handling, and a "you refreshed too fast" state — none of which earn their cost at v1.0 scale
- Easy to add later if a real user signal emerges

### Why not webhooks / real-time
- GitHub state doesn't change minute-to-minute in a way that affects PM decisions
- Webhooks add: endpoint, auth, retry, dedup, queue, replay — meaningful infra
- v1.0 is for 5–8 orgs and a handful of active projects per org — pull-on-demand is sufficient

---

## Consequences

- **ADR-007 supersession** — the `cr-tracker.md` parsing path described in ADR-007 is no longer implemented. ADR-007 must be marked Superseded by ADR-008 (Architect action).
- **ADR-004 unchanged** — ADR-004 already authorises backend GitHub calls; this ADR narrows them to milestones and sprint membership and adds the cache rule.
- **Schema cleanups** — remove `cr_tracker_path` from `projects`, remove `sync_status` and `last_synced_at` from `cr_summaries`.
- **Sync Lambda scope reduction** — if the scheduled sync Lambda from ADR-004 was already planned, its scope should be reduced to only the operations the cache cannot serve (e.g. background warmup if measured to help). Otherwise it can be deferred entirely.
- **Code review checklist** — frontend PRs must not introduce direct GitHub fetches; CR-related code must not reference file paths or repo content.
- **EP-09 (Project Health Dashboard)** — implements this ADR as its first concrete user-facing surface; ST-059 is the reference implementation of the cache rule.

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Scheduled 15-min sync into a mirror table | Heavyweight for two entity types; introduces a "sync is broken" class of incidents we don't need at v1.0 scale |
| Webhook-driven near-real-time | Real infra cost (queue, auth, replay) for marginal user value — sprint state doesn't shift minute-to-minute |
| Frontend calls GitHub directly with user OAuth | Violates the data ownership boundary, leaks org GitHub tokens to the browser, and couples UI to GitHub API shape |
| Keep `cr-tracker.md` parsing alongside CRUD | Two sources of truth, guaranteed to diverge; defeats the purpose of the rejection |

---

## Review Trigger

Revisit this ADR when:
- A user complains about staleness on the dashboard, suggesting 60s is too long
- A new GitHub-mirrored entity is added that doesn't fit the cache model
- Org count grows past ~50 or active projects per org grows past ~20, at which point cache miss patterns may warrant a real mirror
- A new external system (Jira, Linear, etc.) is integrated — this ADR must be extended, not duplicated per system
