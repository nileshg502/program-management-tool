# ST-002 — Create Organisation entity and seed data

**Story ID:** ST-002
**Epic:** EP-01 — Foundation & Authentication
**Priority:** P0
**Points:** 3
**Status:** Not Started

---

## User Story

As a system administrator, I need an Organisation entity in the database with name and logo, so that the tool knows which organisation it belongs to.

---

## Acceptance Criteria

- [ ] Organisation table created with fields: `id`, `name`, `logo_url`, `created_at`
- [ ] Single organisation record seeded via migration or seed script
- [ ] Organisation name displayed in the application header/nav
- [ ] Logo displayed in the application header/nav if logo_url is set
- [ ] Admin can update organisation name and logo via settings screen
- [ ] API returns organisation details at `GET /api/v1/organisation`

---

## Notes

- v1.0 is single-tenant — one organisation per deployment
- Multi-tenancy is explicitly out of scope
