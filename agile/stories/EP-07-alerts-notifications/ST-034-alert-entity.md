# ST-034 — Create Alert entity and notification store

**Story ID:** ST-034
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As the system, I need an Alert entity and an in-app notification store, so that triggered alerts can be persisted and surfaced to the correct user in the notification panel.

---

## Acceptance Criteria

- [ ] Alert table created with fields: `id`, `type`, `project_id`, `sent_to_role`, `sent_to_user_id` (nullable), `message`, `is_read`, `created_at`
- [ ] `type` field accepts: `milestone_behind`, `milestone_at_risk`, `sprint_blocked`, `cr_pending_overdue`, `domain_mismatch`
- [ ] `is_read` defaults to false
- [ ] `GET /api/v1/notifications` returns unread alerts for the authenticated user
- [ ] `PATCH /api/v1/notifications/:id/read` marks an alert as read
- [ ] Unread count available at `GET /api/v1/notifications/count`

---

## Notes

- Alerts are per-user — the same event may generate multiple alert records if multiple users need to be notified
- Alert history is retained indefinitely in v1.0 (no expiry)
