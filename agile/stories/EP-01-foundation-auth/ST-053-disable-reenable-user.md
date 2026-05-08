# ST-053 — Manage User — disable and re-enable user

**Story ID:** ST-053
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a Manager, I need to disable a user without deleting them, so that I can revoke sign-in access while keeping their record and history intact — and re-enable them later if needed.

---

## Acceptance Criteria

- [ ] Manage User drawer (S-15) shows a single action that toggles between "Disable user" and "Re-enable user" based on current status
- [ ] When user `status = active` — action label is "Disable user"; action calls `POST /api/v1/users/:id/disable`
- [ ] When user `status = disabled` — action label is "Re-enable user"; action calls `POST /api/v1/users/:id/enable`
- [ ] Disable action calls Cognito `adminDisableUser`, then sets Trackwise `status = disabled`
- [ ] Enable action calls Cognito `adminEnableUser`, then sets Trackwise `status = active`
- [ ] Disable also calls Cognito `adminUserGlobalSignOut` to revoke any active sessions
- [ ] Disabled users do NOT count toward the "Active" filter or the "X active" summary in the User Management header
- [ ] Disabled users still appear in the "All" filter and the "Disabled" filter on the User Management table
- [ ] Disabled users cannot sign in — Cognito returns `NotAuthorizedException` on login attempt
- [ ] Status badge updates immediately in both the Manage User drawer and the User Management table after toggle
- [ ] On success — toast displays "User disabled — they can no longer sign in" or "User re-enabled — they can sign in again"
- [ ] Audit entry written: `{ action: "user_disabled" | "user_enabled", target_user_id, performed_by_user_id, performed_at }`
- [ ] Caller cannot disable their own account — backend returns `400 Bad Request` with message "You cannot disable your own account"
- [ ] If the target user is the last remaining active Manager in the org — disable is rejected with `400 Bad Request` "At least one active Manager is required per organisation"

---

## Notes

- Disable is a soft, reversible operation. Hard deletion is covered by ST-054.
- Disabled users retain all historical references (project assignments, audit entries, "created by" links). No data is removed.
- The "last active Manager" guard prevents an org from locking itself out. This invariant is checked in the same transaction as the disable to avoid a race condition.
- ADR-003 originally deferred this action to the Cognito console for v1.0. PM approved on 2026-05-07 to bring it in-app for v1.0. Architect to amend ADR-003 to match.
