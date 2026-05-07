# ST-040 — In-app notification panel with unread count badge

**Story ID:** ST-040
**Epic:** EP-07 — Alerts & Notifications
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a user, I need an in-app notification panel with an unread count badge, so that I can see pending alerts without leaving my current page.

---

## Acceptance Criteria

- [ ] Notification bell icon in the navigation bar visible on all pages
- [ ] Unread count badge displayed on the bell icon — shows number of unread alerts
- [ ] Badge disappears when unread count reaches 0
- [ ] Clicking the bell opens a notification panel (slide-out or dropdown)
- [ ] Panel lists alerts newest first — shows message, project name, and relative timestamp ("2 hours ago")
- [ ] Unread alerts visually distinct from read alerts (bold or background highlight)
- [ ] Clicking an alert marks it as read and navigates to the relevant project detail page
- [ ] "Mark all as read" action clears the unread badge
- [ ] Panel shows only alerts for the authenticated user — not other users' alerts

---

## Notes

- Depends on ST-034 (Alert entity and read/count endpoints)
- Real-time push (websocket) is not required for v1.0 — polling every 60 seconds is acceptable
