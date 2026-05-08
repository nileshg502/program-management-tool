# ST-054 — Manage User — delete user (hard delete)

**Story ID:** ST-054
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a Manager, I need to permanently delete a user from Trackwise and Cognito, so that ex-employees and mistakenly created accounts can be fully removed when retention is not required.

---

## Acceptance Criteria

- [ ] Manage User drawer (S-15) exposes a "Delete user" action inside a visually distinct Danger zone
- [ ] Clicking the action shows a confirmation prompt: "Delete user permanently? This cannot be undone."
- [ ] Confirm calls `DELETE /api/v1/users/:id`
- [ ] Backend calls Cognito `adminDeleteUser` for the target user
- [ ] Backend hard-deletes the Trackwise user record after Cognito returns success
- [ ] If Cognito delete fails — Trackwise record is NOT deleted and `502 Bad Gateway` is returned with the Cognito error
- [ ] Foreign-key references (e.g. `created_by_user_id` on user records, audit entries, project assignments) are preserved and continue to display the deleted user's name as "[deleted user]"
- [ ] Project assignments owned by the deleted user are NOT removed — assignments persist on the project for historical accuracy; the team list shows "[deleted user]" until a Manager updates it
- [ ] Caller cannot delete their own account — backend returns `400 Bad Request` "You cannot delete your own account"
- [ ] If target user is the last active Manager in the org — delete is rejected with `400 Bad Request` "At least one active Manager is required per organisation"
- [ ] On success — drawer closes, target row is removed from User Management table, and toast displays "User deleted"
- [ ] Audit entry written: `{ action: "user_deleted", target_user_id, target_email, target_full_name, performed_by_user_id, performed_at }` — the email and name are captured at deletion time so the audit log is interpretable after the user record is gone

---

## Notes

- Hard delete is irreversible. Soft disable (ST-053) is the recommended default; delete is for cases where retention is not required.
- "[deleted user]" placeholder ensures team rosters and audit timelines remain readable after deletion.
- ADR-003 originally deferred this action to the Cognito console for v1.0. PM approved on 2026-05-07 to bring it in-app for v1.0. Architect to amend ADR-003 to match.
- An organisation-wide audit log view is out of scope for this story; the audit row is written so a future audit-log feature can query it.
