# ADR-011 — GitHub Mirror Cache

**Date:** 2026-05-08
**Status:** Approved
**Decider:** Architect

---

## Context

[ADR-008](ADR-008-data-ownership-and-sync.md) established the rule: GitHub is the system of record for milestones and sprint membership; the frontend never calls GitHub; the backend reads on-demand and caches server-side with a 60-second TTL. No scheduled sync, no mirror table, no webhooks for v1.0.

This ADR specifies the **mechanism** — what collections hold the cache, what fields, what TTL behaviour, what happens on cache miss / GitHub failure / token expiry.

This ADR is referenced by:
- ADR-002 (collections registered)
- ADR-008 (decision rule)
- ADR-010 (operational entities — explicitly excludes GitHub data)
- ST-059 (Project Health API — primary consumer)
- ST-045 (sync sprint label issues — note: this story name predates ADR-008; it now means "fetch sprint label issues" not "sync to mirror")

---

## Decision

### Two cache collections

| Collection | Cache Granularity | Source GitHub API |
|------------|-------------------|-------------------|
| `github_cache_milestones` | Per-project | `GET /repos/{owner}/{repo}/milestones?state=all` |
| `github_cache_sprint_membership` | Per-(project, sprint label) | `GET /repos/{owner}/{repo}/issues?labels=sprint-N&state=all` |

These are the **only** GitHub-derived data persisted in Trackwise. Everything else (commits, PRs, comments, code) is read on demand within a single API call boundary if ever needed; not cached, not stored. Currently, no other GitHub data is read.

---

## Schema — `github_cache_milestones`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | Tenant scope |
| `project_id` | ObjectId | Yes | FK → `projects._id` (one cache row per project) |
| `payload` | Object | Yes | The processed milestone array — see Payload Shape below |
| `fetched_at` | Date | Yes | Wall-clock time when this row was written |
| `expires_at` | Date | Yes | `fetched_at + 60 seconds`. Indexed with TTL — Mongo auto-purges expired rows |
| `source_etag` | String | No | `ETag` header from GitHub response — enables conditional revalidation |
| `source_status` | Enum | Yes | `ok` \| `partial` \| `stale_serving` — see Failure Modes below |
| `source_error` | String | No | Last error string when `source_status ≠ ok` |

**Indexes:**
- `org_id + project_id` (unique compound) — one cache row per project at a time
- `expires_at` with `expireAfterSeconds: 0` — Mongo TTL index, automatic deletion

### Payload Shape

```json
{
  "milestones": [
    {
      "github_id": 12345,
      "name": "v1.0 Launch",
      "due_date": "2026-06-30T00:00:00Z",
      "state": "open",
      "open_count": 8,
      "closed_count": 24,
      "url": "https://github.com/acme/portal-revamp/milestone/3"
    }
  ]
}
```

Field selection is deliberate — only what dashboard renders is stored. The full GitHub response is **not** persisted (extra fields, smaller payload, no accidental dependencies on GitHub schema).

---

## Schema — `github_cache_sprint_membership`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `_id` | ObjectId | Yes | |
| `org_id` | ObjectId | Yes | |
| `project_id` | ObjectId | Yes | FK → `projects._id` |
| `sprint_label` | String | Yes | e.g. `sprint-5` (matches PRD §12.1 pattern) |
| `payload` | Object | Yes | Issue array — see Payload Shape below |
| `fetched_at` | Date | Yes | |
| `expires_at` | Date | Yes | `fetched_at + 60 seconds`. TTL-indexed |
| `source_etag` | String | No | |
| `source_status` | Enum | Yes | `ok` \| `partial` \| `stale_serving` |
| `source_error` | String | No | |

**Indexes:**
- `org_id + project_id + sprint_label` (unique compound) — one row per (project, sprint label)
- `expires_at` with `expireAfterSeconds: 0` — TTL

### Payload Shape

```json
{
  "issues": [
    {
      "github_id": 678,
      "number": 142,
      "title": "Implement password reset flow",
      "state": "closed",
      "labels": ["sprint-5", "type:story", "blocked"],
      "type": "story",
      "is_blocked": false,
      "closed_at": "2026-04-22T10:14:00Z",
      "url": "https://github.com/acme/portal-revamp/issues/142"
    }
  ],
  "counts": {
    "committed": 18,
    "completed": 16,
    "blocked": 1,
    "carryover": 3,
    "fresh": 15
  }
}
```

Notes:
- `type` is derived from labels (`type:story`, `type:bug`, `type:epic`). Label convention is PM-parked; for v1.0 the parser tolerates several conventions and returns `null` for `type` if no convention matches.
- `is_blocked` is `true` if any label exactly matches `blocked` (case-sensitive — matches GitHub's default convention).
- `counts.carryover` is set when the same issue carries any earlier `sprint-K` label where K < the row's `sprint_label` numeric suffix. Requires the append-only label rule (PRD §12.1).

---

## Cache lifecycle

### Read path (cache hit)

1. Service receives `getMilestones(project_id)` or `getSprintMembership(project_id, sprint_label)`.
2. Mongo query: cache row where `expires_at > now()`.
3. If found — return `payload` immediately.
4. Average expected hit: well above 90% during dashboard usage bursts (60s TTL covers refresh patterns).

### Read path (cache miss)

1. Cache miss or TTL expired.
2. Fetch from GitHub using the project's stored OAuth token (per ADR-004).
3. Optional: send `If-None-Match: <stored_etag>` header. If GitHub responds `304 Not Modified`, reuse the existing payload, just update `fetched_at` and `expires_at`.
4. On `200 OK`: parse, derive counts, upsert the cache row with new `payload`, `fetched_at`, `expires_at`, `source_etag`, `source_status: ok`.
5. Return payload.

### Write path

There is no user-facing write path to these collections. They are populated only by cache fetches. Manual cache invalidation is **not exposed** in v1.0 (no manual refresh button per ADR-008).

### TTL purge

- Mongo TTL monitor purges expired rows ~every 60 seconds (Mongo's default sweep interval).
- Stale rows are not actively served — the read path checks `expires_at` even before TTL purge runs.
- TTL is a cleanup mechanism, not the freshness contract.

---

## Failure modes

GitHub is the upstream dependency. Three failure classes; each has a defined response.

### Class 1 — GitHub returns successfully

`source_status: ok`. Normal flow.

### Class 2 — GitHub returns an error (5xx, rate-limit, network timeout)

- If a **previous cache row exists for this key** (regardless of `expires_at`):
  - Serve the stored payload with `source_status: stale_serving`
  - Update `source_error` with the failure reason
  - Do not extend `expires_at` — the row remains expired and the next request will retry GitHub
  - Dashboard payload includes `data_available: true, freshness: "stale", reason: <error>`

- If **no cache row exists** for this key (cold cache, first request):
  - Return no payload
  - Dashboard payload sets `data_available: false, reason: "github_unavailable"`
  - Affected dashboard section renders an inline message; rest of the page still loads (per ST-059 AC)

### Class 3 — OAuth token expired or revoked

- GitHub returns `401 Unauthorized`.
- Cache write skipped; `source_status` set to `partial` on the existing row if any.
- Dashboard surfaces a project-level banner: "GitHub authentication needs to be reconnected. PM action required."
- Reconnect flow is a separate user journey (per ADR-004 OAuth flow); not in scope here.

### Rate limiting

- GitHub authenticated rate limit: 5,000 requests/hour per OAuth token.
- Worst case at v1.0 scale: 8 orgs × 10 projects × ~12 dashboard loads/hour × 2 endpoints × ~8 sprint labels per project = ~15,360 reqs/hour. Above limit only if every project is hammered simultaneously — implausible at this scale; the 60s cache absorbs most repeated loads from the same user.
- Headroom revisit when org count exceeds 30 or projects per org exceed 20.

---

## NestJS service contract

```typescript
// Illustrative — not authoritative implementation
interface GitHubCacheService {
  getMilestones(orgId: ObjectId, projectId: ObjectId): Promise<MilestonesPayload | NotAvailable>;
  getSprintMembership(orgId: ObjectId, projectId: ObjectId, sprintLabel: string): Promise<SprintMembershipPayload | NotAvailable>;
}

type NotAvailable = { data_available: false; reason: string };
```

The cache service is the **only** code path permitted to call GitHub for these two endpoints. Other modules consume via this service; they never construct GitHub clients themselves.

---

## Rationale

### TTL on `expires_at` field over polling-based purge
- Mongo native TTL is free, requires no application code, runs in the cluster.
- Atlas M0 supports TTL indexes — verified in MongoDB Atlas docs.
- Application-side purge would need a cron job or Lambda — exactly the infrastructure ADR-008 avoided.

### Single cache row per project per key (upsert)
- Cache is freshness-only — no historical replay value.
- Upsert is simpler and cheaper than insert + cleanup.
- Unique compound index prevents concurrent writes from creating duplicates.

### ETag conditional revalidation
- GitHub's `ETag` header lets us detect "nothing changed" with a free request (doesn't count against rate limit if `304 Not Modified`).
- Reduces GitHub API consumption when the same project is re-checked frequently.
- Optional — implementation can ship without ETag handling and add later.

### `stale_serving` instead of refusing on GitHub errors
- A stale dashboard is better than a broken dashboard.
- The `source_status` and dashboard `freshness: "stale"` flags let the user know the data may be slightly out of date.
- Pure failure mode (no payload at all) only kicks in for cold cache — when there is genuinely no data to show.

### Selective field extraction (not raw GitHub response)
- Stores only what the dashboard renders — smaller documents, faster reads.
- Decouples Trackwise from GitHub schema drift — adding/removing fields in GitHub's response doesn't affect cache structure.
- Easier to debug — payload is human-readable in Atlas Compass.

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| In-memory cache (Lambda-local) | Lambda instances are short-lived and many; cache hit rate would be near zero |
| Redis (ElastiCache) | Adds infra cost and complexity; Mongo TTL achieves the same outcome at v1.0 scale |
| Long-lived mirror table (no TTL, refreshed on schedule) | Exactly what ADR-008 rejected — drift, sync infrastructure |
| Cache full GitHub response | More storage, exposes Trackwise to GitHub schema changes, no benefit |
| Per-issue cache rows | More granular but vastly more rows; query cost dominates |
| Webhook-driven cache invalidation | Adds GitHub webhook receiver, signature verification, retry queue — significant infra for marginal latency gain at v1.0 scale |
| Skip caching, fetch on every request | Burns GitHub rate limit, slow dashboard loads (300–500ms GitHub round-trip per call) |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| 60s TTL is fixed (not configurable per project) | Acceptable at v1.0 — see ADR-008 rationale | A user complaint or measured staleness incident |
| Cold cache on first request after TTL purge incurs GitHub round-trip | Stale-serving on GitHub failure mitigates the worst case | Latency budget tightens |
| TTL purge runs on Mongo's schedule (~60s sweep) | Read-side `expires_at` check ensures correctness even before purge | TTL becomes a correctness concern (it shouldn't) |
| ETag handling is optional and not in initial implementation | Rate limit headroom is large at v1.0 | Approaching rate limit — add ETag |
| No backfill on cold cache | Cold start is rare; one slow dashboard load is acceptable | Cold-start frequency increases (e.g. heavy autoscaling) |
| Cross-region latency to GitHub | Lambda is in `us-east-1` (or wherever deployed); GitHub is global | Region-specific latency complaints |

---

## Consequences

- A new NestJS module `github-cache` is required, exposing the service contract above.
- ADR-002 amendment registers the two collections; this ADR defines them.
- Atlas M0 TTL index support must be verified before deploy (it is — referenced in MongoDB Atlas TTL docs).
- The dashboard Health API (ST-059) reads via the cache service — never directly from GitHub.
- ST-045 (sync sprint label issues) is **redefined**: it no longer creates a Trackwise `sprints` collection; instead it implements the cache fetch + parse logic. Story content needs updating to match (BA action — SESSION-STATE follow-up).
- Any future GitHub-derived data (e.g. blocked-label counts beyond what's in the issue payload) extends one of the existing cache collections rather than adding a third — keeps the cache surface narrow.
- Encryption-at-rest for cache rows: relies on Atlas at-rest encryption (default on Atlas tiers ≥ M10; M0 has shared encryption). No additional application-level encryption — cache contents are not sensitive (issue titles + counts are not PII or secrets).

---

## Operations / Monitoring

- CloudWatch alarms on:
  - Cache miss rate above 50% sustained — indicates TTL too aggressive or many distinct sprints/projects
  - GitHub `5xx` rate above 1% — upstream issue
  - GitHub `401` rate above 0.1% — token expiry trend
- Mongo Atlas alerts on:
  - TTL deletion volume — sanity check that TTL is purging
  - Collection size — early warning before storage limits

---

## Review Trigger

Revisit this ADR when:
- Org count or projects per org grows enough to challenge GitHub rate limits
- A second external system is integrated (Jira, Linear) — extend, don't duplicate, the cache pattern
- Real-time freshness becomes a user requirement — evaluate webhooks
- Atlas M0 TTL behaviour changes (unlikely; documented contract)
- Manual refresh UX is added (would require cache-bust capability)
