# ST-057 — Manage Client — delete client (cascade hard delete)

**Story ID:** ST-057
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a Manager, I need to permanently delete a client and all of its associated projects, so that test clients and engagements that should never have existed can be fully removed.

---

## Acceptance Criteria

- [ ] Manage Client drawer (S-16) exposes a "Delete client" action inside the Danger zone
- [ ] Clicking the action shows a confirmation prompt: "Delete this client and all its projects? This cannot be undone."
- [ ] Confirm calls `DELETE /api/v1/clients/:id`
- [ ] Backend hard-deletes the client record AND every project record where `project.client_id = :id`
- [ ] For each cascade-deleted project, the following dependent records are also hard-deleted within the same transaction:
  - Project assignments (employee–project links)
  - Forensic CR Summary records
  - Sync state records (last sync timestamp, cached milestone data, cached sprint data)
  - Tracked-file configuration records
  - Alert history rows scoped to those projects
- [ ] GitHub OAuth tokens linked to deleted projects are revoked via the GitHub API where possible; deletion proceeds even if revocation fails (revocation failure logged to CloudWatch)
- [ ] Audit entry written for the client AND for each cascade-deleted project, capturing name and id at deletion time so the audit log remains interpretable
- [ ] Employee records are NOT deleted — assignments to deleted projects vanish but employees and their domain knowledge remain
- [ ] Operation is rejected with `400 Bad Request` "Client has active projects with sync running — archive first" when any project under the client has a sync currently in flight
- [ ] On success — drawer closes, client disappears from all views, and toast displays "Client deleted"
- [ ] Operation is irreversible — confirmation copy must communicate this clearly

---

## Notes

- Hard delete is intentionally aggressive. Archive (ST-056) is the recommended default; delete is for cases where retention is not required.
- "Sync in flight" guard prevents race conditions where the sync Lambda writes to records being deleted. The check uses the sync state record's `running` flag.
- An organisation-wide audit log view is out of scope for this story; rows are written so a future audit feature can query them.
- The 5-point estimate reflects the cascade scope, not UI complexity — the UI surface is a single button + confirm.
