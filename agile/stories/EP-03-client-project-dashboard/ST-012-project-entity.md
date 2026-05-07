# ST-012 — Create Project entity linked to Client

**Story ID:** ST-012
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As an admin or PM, I need to create and manage project records linked to a client, so that delivery work is tracked under the correct client.

---

## Acceptance Criteria

- [ ] Project table created with fields: `id`, `name`, `code`, `client_id`, `delivery_type`, `go_live_date`, `github_repo_url`, `status`, `created_at`, `updated_at`
- [ ] `delivery_type` accepts: `fixed_scope` or `fixed_budget`
- [ ] `status` accepts: `active`, `on_hold`, `completed`, `archived`
- [ ] `code` is unique across all projects — enforced at DB level
- [ ] Admin/PM can create a new project linked to a client
- [ ] Admin/PM can edit all project fields
- [ ] `GET /api/v1/projects` returns all active projects
- [ ] `GET /api/v1/projects/:id` returns full project detail
- [ ] Project is accessible to PM only if they are assigned to it (RBAC from ST-005)

---

## Notes

- `github_repo_url` is optional at creation — can be added later when GitHub integration is configured (EP-08)
- Project `code` is used in forensic CR IDs — format defined in Forensic Role documentation
