# ADR-006 — Notification and Alert Delivery

**Date:** 2026-05-06
**Status:** Approved
**Decider:** Architect + PM

---

## Context

The PRD defines 5 alert triggers that notify relevant users when a threshold is crossed on a project. Alerts must be shown in-app and have a history per project. This ADR defines how alerts are detected, stored, and displayed.

---

## Decision

### Delivery Channel
In-app only. No email notifications in v1.0.

- Notification bell icon in the top navigation bar
- Unread alert count badge on the bell
- Clicking the bell opens a notification panel — all unread alerts across all projects
- Alert history accessible per project in the project detail view

### Alert Detection
The sync Lambda evaluates all alert conditions immediately after updating milestone and sprint data for a project. No separate alert Lambda.

Evaluation runs on every sync cycle — scheduled and on-demand.

### Alert Storage
Alerts stored in the `alerts` collection in MongoDB (defined in ADR-002).

Each alert document:

| Field | Type | Description |
|-------|------|-------------|
| `_id` | ObjectId | Unique alert ID |
| `org_id` | String | Tenant identifier |
| `project_id` | ObjectId | Project the alert belongs to |
| `type` | Enum | Alert type — see trigger table below |
| `detail` | Object | Alert-specific metadata (e.g. milestone name, blocked count) |
| `sent_to` | String[] | User IDs of recipients |
| `status` | Enum | `unread` / `read` |
| `created_at` | Date | When the alert was generated |

### Alert Triggers

| Type | Condition | Recipients |
|------|-----------|-----------|
| `MILESTONE_BEHIND` | Milestone status changes from On Track → Behind | All Managers in the org |
| `MILESTONE_AT_RISK` | Milestone status changes from On Track → At Risk | All Managers in the org |
| `SPRINT_BLOCKED` | Sprint blocked story count exceeds 3 | All Managers in the org |
| `FORENSIC_CR_PENDING` | A CR has been in Pending status for more than 1 sprint duration | All Managers in the org |
| `DOMAIN_MISMATCH` | A team member has no domain knowledge matching any project domain | All Managers in the org |

### Deduplication
- Alert is only created if no unread alert of the same `type` exists for the same `project_id`
- Prevents duplicate alerts on every sync cycle for a condition that has not been resolved
- When condition resolves — existing alert is marked `resolved` (soft close), new alert can be raised if condition recurs

### Read / Unread
- User opens notification panel → all visible alerts marked `read` for that user
- Unread count on bell updates via Redux store after panel is opened
- Alert history per project shows all alerts regardless of read status, sorted by `created_at` descending

---

## Rationale

### In-app only — no email
- PRD specifies in-app notifications — email is not a v1.0 requirement
- Avoids AWS SES setup, email template management, and unsubscribe compliance (CAN-SPAM / GDPR) in v1.0
- Email can be added later without architecture change — alert records are already stored, delivery channel is the only addition

### Sync Lambda evaluates alerts
- Sync Lambda already has the updated milestone and sprint data in memory after each sync
- Evaluating alerts in the same Lambda avoids a second read of the same data
- No extra Lambda, no extra EventBridge rule, no extra cost
- If sync fails — alert evaluation does not run, which is correct (no new data = no new alert evaluation)

### Deduplication by type + project_id
- Without deduplication, every sync cycle would generate a new alert for an unresolved condition
- Notification bell would fill with duplicates — reduces trust in the system
- Single unread alert per condition per project keeps the signal clean

### All Managers receive all alerts
- RBAC has a single Manager role (ADR-003) — no filtering by role needed
- All Managers are responsible for project health — all should see all alerts

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Email notifications | Not a v1.0 requirement — adds SES setup and compliance overhead |
| Separate alert evaluation Lambda | Adds infrastructure and cost; sync Lambda already holds the data needed |
| WebSocket push to frontend | Real-time push not required — alerts are visible on next page load or navigation; no background refresh on frontend (ADR-005) |
| Per-user alert targeting (CTO vs PM) | Single Manager role — no targeting needed in v1.0 |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| Alerts only evaluated during sync — not real-time | Acceptable — sync runs every 5–60 min; on-demand sync available | Real-time alert requirement emerges |
| No email delivery | In-app bell is sufficient for v1.0 | User feedback requests email notifications |
| No mobile push notifications | Out of scope — no mobile app in v1.0 | Mobile app added |
| `FORENSIC_CR_PENDING` requires sprint duration to be known | Sprint start date stored in `sprints` collection — sync Lambda compares CR pending date against sprint duration | Sprint duration tracking becomes complex |

---

## Consequences

- Sync Lambda must compare previous milestone/sprint state against new state to detect transitions (On Track → Behind, On Track → At Risk)
- Previous state must be stored per milestone and sprint in MongoDB — sync Lambda reads old status, writes new status, compares to determine if alert is needed
- Frontend notification bell must fetch unread alert count on app load via RTK Query
- Alert panel fetches full alert list on open — paginated if count is large
- `alerts` collection index on `org_id + project_id + status` already defined in ADR-002

---

## Review Trigger

Revisit this ADR when:
- Email notification requirement is raised
- Real-time alert delivery is required
- Role-based alert targeting is needed (e.g. only PM sees sprint alerts, only CTO sees milestone alerts)
