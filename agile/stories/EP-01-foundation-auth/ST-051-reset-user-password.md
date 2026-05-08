# ST-051 — Manage User — reset password (admin-initiated)

**Story ID:** ST-051
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a Manager, I need to reset another user's password from inside Trackwise, so that I can restore access without leaving the app or opening the AWS console.

---

## Acceptance Criteria

- [ ] Manage User drawer (S-15) exposes a "Reset password" action for any active user
- [ ] Action triggers `POST /api/v1/users/:id/reset-password`
- [ ] Backend calls Cognito `adminResetUserPassword` for the target Cognito user
- [ ] Cognito then issues a temporary password and forces password change on next sign-in
- [ ] Backend response includes whether the temporary password was emailed by Cognito or must be communicated out-of-band — depending on the User Pool email configuration
- [ ] On success — drawer closes and toast displays "Temporary password sent to user"
- [ ] Audit entry written: `{ action: "user_password_reset", target_user_id, performed_by_user_id, performed_at }`
- [ ] Audit entry surfaces in the target user's "Recent activity" timeline as "Password reset by [Manager name]"
- [ ] Action is disabled (greyed out, with tooltip "User is disabled — re-enable first") when target user `status = disabled`
- [ ] Caller cannot reset their own password via this action — for self-service use the in-app change-password flow (out of scope of this story)
- [ ] Target user's existing sessions are revoked — backend calls Cognito `adminUserGlobalSignOut` after the reset
- [ ] Cognito error is surfaced to the caller as a toast with the Cognito error code (e.g. "UserNotFoundException", "NotAuthorizedException")

---

## Notes

- ADR-003 originally deferred this action to the AWS Cognito console for v1.0. PM approved on 2026-05-07 to bring it in-app for v1.0. Architect to amend ADR-003 to match — amendment is a documentation follow-up, not a gate on this story.
- Cognito User Pool must have an email-sending configuration (SES or Cognito default) for the temporary password to reach the user automatically. Without it, the Manager must communicate the password directly — wording in the toast will need adjustment.
- Whether to email the user a notification of the reset event is a separate concern handled by EP-07 alerts (not in this story).
