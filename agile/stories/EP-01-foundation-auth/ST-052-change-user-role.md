# ST-052 — Manage User — change user role

**Story ID:** ST-052
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P2
**Points:** 2
**Status:** Not Started

---

## User Story

As a Manager, I need to change another user's organisational role between CTO/CEO and PM, so that role labels stay accurate as people move within the organisation.

---

## Acceptance Criteria

- [ ] Manage User drawer (S-15) exposes a "Change role" action that opens the Change Role modal
- [ ] Modal lists exactly two options: `cto_ceo`, `pm` (display labels "CTO / CEO", "PM")
- [ ] Modal pre-selects the user's current role
- [ ] Save calls `PATCH /api/v1/users/:id` with body `{ role: "cto_ceo" | "pm" }`
- [ ] Backend updates the `role` column on the user record
- [ ] Backend updates the Cognito user's custom attribute `custom:role` via `adminUpdateUserAttributes`
- [ ] If Cognito update fails — Trackwise update is rolled back and `502 Bad Gateway` returned
- [ ] On success — modal closes, drawer refreshes, role badge updates, and toast displays "Role updated"
- [ ] Audit entry written: `{ action: "user_role_changed", target_user_id, from_role, to_role, performed_by_user_id, performed_at }`
- [ ] Audit entry surfaces in the target user's "Recent activity" timeline as "Role changed: [from] → [to] by [Manager name]"
- [ ] Modal includes the explanatory text "Both roles share the same permissions in v1.0." (per ADR-003)
- [ ] Caller may change their own role — no self-edit guard needed since both roles share permissions

---

## Notes

- ADR-003 makes both roles permission-equivalent under the single application role "Manager" — this story keeps the organisational label in sync; it does not change any access path.
- The role label is informational in v1.0. If permission divergence is introduced in a later ADR, this story may need re-scoping to also adjust group membership in Cognito.
- The modal is reused from S-15; no new modal is required.
