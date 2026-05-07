# ST-011 — Create Client entity and admin CRUD

**Story ID:** ST-011
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As an admin, I need to create and manage client records, so that projects can be linked to the correct client.

---

## Acceptance Criteria

- [ ] Client table created with fields: `id`, `name`, `industry`, `created_at`, `updated_at`
- [ ] Admin can create a new client record
- [ ] Admin can edit client name and industry
- [ ] Admin can deactivate a client — projects remain but client is hidden from active dashboard
- [ ] `GET /api/v1/clients` returns all active clients with their active project count
- [ ] `GET /api/v1/clients/:id` returns a single client with their projects list

---

## Notes

- Industry is free text — no fixed list
- Active project count is a computed field — derived from linked projects where status is not archived
