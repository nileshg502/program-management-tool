# ST-055 — Manage Client — edit client details

**Story ID:** ST-055
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As a Manager, I need to edit a client's name, industry, primary contact, status, and notes from the Manage Client drawer, so that client records stay accurate as the relationship evolves.

---

## Acceptance Criteria

- [ ] Manage Client drawer (S-16) is opened from the Client Management table by clicking a row or its "Manage" button
- [ ] Drawer shows the client identity (name, industry, status, added date) and KPI tiles for active project count and total hours
- [ ] Editable fields: `name`, `industry`, `status`, `primary_contact_email`, `notes`
- [ ] `industry` selector lists: FinTech, HealthCare, eCommerce, Logistics, Other (matches mockup)
- [ ] `status` selector lists: `active`, `inactive` (display labels "Active", "Inactive")
- [ ] `primary_contact_email` validated as RFC 5322 format on client and server — empty value allowed
- [ ] `notes` is free-text, capped at 2,000 characters
- [ ] Save calls `PATCH /api/v1/clients/:id` with the changed fields only (snake_case body)
- [ ] Backend rejects rename if another active client in the same org already has that name — returns `409 Conflict` "A client with this name already exists"
- [ ] On success — drawer closes, table row reflects updated values, and toast displays "Client updated"
- [ ] Status change from `active` → `inactive` is equivalent to archive (see ST-056 for archive semantics) — not a separate operation
- [ ] Each saved change appends an entry to the client's activity timeline: `{ action, field_name, from_value, to_value, performed_by_user_id, performed_at }`
- [ ] KPI tiles (active projects, total hours) are read-only and computed server-side — not editable in this story
- [ ] Drawer closes on Esc, click-outside, or Cancel without saving

---

## Notes

- ST-011 already covers the underlying Client entity. This story is the edit-from-drawer UI on top of it; ST-011's API surface (`GET /api/v1/clients`) does not need changing for this story.
- Total hours is a computed field summing project totals — story is descriptive only; the formula is owned by an EP-04 / EP-08 story when project hours are tracked.
- Activity timeline is rendered in the drawer's "Recent activity" section. The activity store schema will be reused by ST-056 and ST-057.
