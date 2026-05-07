# ADR-007 — Forensic Data Source

**Date:** 2026-05-06
**Status:** Approved
**Decider:** Architect + PM

---

## Context

The PRD requires a Forensic Summary on every project card and project detail view showing total CRs logged, absorbed, converted, pending, and removed counts and hours. The source of this data is the `cr-tracker.md` file maintained by the Forensic Role and committed to the project's GitHub repository. This ADR defines how Trackwise reads, parses, and stores this data.

---

## Decision

### Data Source
`cr-tracker.md` committed inside the project's GitHub repository. Read via GitHub Contents API during each sync cycle.

### File Location
Configurable per project. PM enters the relative path to `cr-tracker.md` when creating or editing a project in Trackwise (e.g. `forensic/cr-tracker.md` or `projects/alpha/forensic/cr-tracker.md`).

Stored in the `projects` collection as `cr_tracker_path`.

### Parsing Strategy
The Totals table in `cr-tracker.md` has a fixed, consistent structure:

```
| Category                  | Count | Hours |
|---------------------------|-------|-------|
| Total CRs Logged          | 0     | 0h    |
| Absorbed (delivered free) | 0     | 0h    |
| Converted (billed)        | 0     | 0h    |
| Pending (decision due)    | 0     | 0h    |
| Removed                   | 0     | 0h    |
```

Sync Lambda reads the file content via GitHub API, extracts the Totals table rows by line matching on the Category label, and parses Count and Hours values. Parsed values are written to the `cr_summaries` collection in MongoDB (defined in ADR-002).

### Sync Trigger
Forensic data is read and parsed during the same sync cycle as GitHub milestone and sprint data. No separate sync for forensic. On-demand sync also refreshes forensic data.

### Missing File Handling
- If `cr_tracker_path` is not configured on a project — forensic summary shows "Forensic tracking not initiated" on dashboard and project detail view
- If path is configured but file is not found in the repo — forensic summary shows "CR tracker not found at configured path" with the path displayed
- If file is found but Totals table cannot be parsed — forensic summary shows "CR tracker format unreadable — check file structure"

---

## cr_summaries Document Structure

| Field | Type | Source |
|-------|------|--------|
| `org_id` | String | Tenant context |
| `project_id` | ObjectId | Project reference |
| `total_count` | Number | Totals table — Total CRs Logged / Count |
| `absorbed_count` | Number | Totals table — Absorbed / Count |
| `absorbed_hours` | Number | Totals table — Absorbed / Hours |
| `converted_count` | Number | Totals table — Converted / Count |
| `converted_hours` | Number | Totals table — Converted / Hours |
| `pending_count` | Number | Totals table — Pending / Count |
| `pending_hours` | Number | Totals table — Pending / Hours |
| `removed_count` | Number | Totals table — Removed / Count |
| `last_synced_at` | Date | Timestamp of last successful parse |
| `sync_status` | Enum | `ok` / `file_not_found` / `parse_error` / `not_configured` |

---

## Rationale

### GitHub Contents API over manual entry
- `cr-tracker.md` is already committed to the repo as part of the Forensic Role workflow
- Reading it via GitHub API requires no extra tooling — OAuth token from ADR-004 covers file reads
- Eliminates double-entry — Forensic Role updates the file, Trackwise reads it automatically on next sync
- Manual entry would create two sources of truth and diverge over time

### Configurable path over fixed convention
- Different projects may organise their repo differently
- Enforcing a fixed path would require all existing projects to restructure their repos
- PM sets the path once at project setup — no ongoing maintenance

### Parse Totals table only
- The Totals table is the only data needed for the dashboard forensic summary
- The All CRs table contains individual CR records — not needed for the summary view
- Parsing only the Totals table reduces parse complexity and failure surface

### Sync Lambda handles forensic parsing
- Forensic data changes only when a CR is logged or decided — infrequent relative to sync interval
- No need for a dedicated forensic sync — bundling with the existing sync Lambda keeps infrastructure minimal
- If sync fails — forensic data is also stale, which is consistent with milestone and sprint data behaviour

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Manual CR summary entry in Trackwise | Creates two sources of truth — file and database diverge; extra PM overhead |
| Fixed file path convention | Forces all project repos to adopt a specific folder structure; breaks existing projects |
| Parse full All CRs table | More data than needed for v1.0 summary view; increases parse complexity and failure risk |
| Separate forensic sync Lambda | Adds infrastructure with no benefit — same GitHub token, same sync cadence |
| JSON format instead of Markdown | cr-tracker.md is a Markdown file by design — Forensic Role maintains it as a human-readable document; format change would require Forensic Role redesign |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| Parsing depends on fixed Totals table format | Format is defined in the Forensic Role template — any change to the template breaks parsing | Forensic Role template is updated — sync Lambda parser must be updated in step |
| Hours values stored as strings in file (e.g. "4h") | Sync Lambda strips "h" suffix and parses as number | Format changes in the template |
| File read via GitHub API counts against rate limit | One extra API call per project per sync — negligible at 5–8 orgs | Org count exceeds 50 |
| No individual CR detail in Trackwise v1.0 | Summary counts only — full CR log remains in the Markdown file | Requirement for CR drill-down in Trackwise UI |

---

## Consequences

- `projects` collection must store `cr_tracker_path` — nullable string, set by PM at project setup
- `cr_summaries` collection must store `sync_status` so frontend can display appropriate message when file is missing or unparseable
- Sync Lambda must handle GitHub API 404 gracefully when file path is wrong — update `sync_status` to `file_not_found` without failing the full sync
- Forensic Role template (`cr-tracker.md`) is now a contract — any structural change to the Totals table must be coordinated with Engineering to update the parser
- Frontend forensic summary component must handle all four `sync_status` values: `ok`, `file_not_found`, `parse_error`, `not_configured`

---

## Review Trigger

Revisit this ADR when:
- Individual CR drill-down is required in Trackwise UI
- Forensic Role template Totals table structure changes
- File-based report reading is expanded to other report types (compliance, security — deferred enhancement from ADR-004)
