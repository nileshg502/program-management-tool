# Data Model — Trackwise

**Last Updated:** 2026-05-06
**Status:** Approved
**Version:** v1.0

---

## Overview

Single MongoDB database. All collections carry `org_id` on every document for multi-tenant isolation. All queries are scoped to `org_id` at the NestJS service layer.

See [ADR-002](../decisions/architect/ADR-002-database-and-data-layer.md) for full rationale.

---

## Collections

### organisations
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `name` | String | Organisation name |
| `logo` | String | Logo URL |
| `github_sync_interval_minutes` | Number | Default 15, range 5–60 |
| `created_at` | Date | |

---

### clients
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `name` | String | Client name |
| `industry` | String | Industry sector |
| `created_at` | Date | |

---

### projects
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `client_id` | ObjectId | Reference to clients |
| `name` | String | Project name |
| `code` | String | Short project code (e.g. TRW) |
| `delivery_type` | Enum | `fixed_scope` / `fixed_budget` |
| `go_live_date` | Date | |
| `github_org` | String | GitHub organisation name |
| `github_repo` | String | GitHub repository name |
| `github_oauth_token` | String | Encrypted OAuth token |
| `github_sprint_label_prefix` | String | e.g. `sprint-` or `Sprint ` |
| `cr_tracker_path` | String | Relative path to cr-tracker.md in repo — nullable |
| `last_synced_at` | Date | Timestamp of last successful sync |
| `last_sync_status` | Enum | `ok` / `failed` / `never` |
| `created_at` | Date | |

---

### employees
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `name` | String | Full name |
| `role` | Enum | `engineer` / `qa` / `ba` / `architect` / `devops` / `pm` |
| `current_assignment_project_id` | ObjectId | Nullable — reference to projects |
| `availability_status` | Enum | `available` / `assigned` / `partially_available` |
| `created_at` | Date | |

---

### domain_knowledge
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `employee_id` | ObjectId | Reference to employees |
| `domain` | String | e.g. FinTech, HealthCare, eCommerce, Logistics |
| `years_of_experience` | Number | Self-declared |
| `created_at` | Date | |

---

### milestones
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `project_id` | ObjectId | Reference to projects |
| `github_milestone_id` | Number | GitHub milestone number |
| `name` | String | Milestone name |
| `due_date` | Date | From GitHub |
| `open_issues` | Number | From GitHub |
| `closed_issues` | Number | From GitHub |
| `percent_complete` | Number | Calculated: closed / (open + closed) * 100 |
| `status` | Enum | `on_track` / `at_risk` / `behind` |
| `previous_status` | Enum | Previous sync status — used for alert detection |
| `created_at` | Date | |

---

### sprints
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `project_id` | ObjectId | Reference to projects |
| `number` | Number | Sprint number |
| `name` | String | Sprint name |
| `github_label` | String | Full label used to query GitHub Issues API |
| `stories_committed` | Number | Total issues with sprint label |
| `stories_complete` | Number | Closed issues with sprint label |
| `stories_in_progress` | Number | Open issues with sprint label, not blocked |
| `blocked_count` | Number | Issues with sprint label + blocked label |
| `start_date` | Date | Used for FORENSIC_CR_PENDING alert evaluation |
| `created_at` | Date | |

---

### cr_summaries
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `project_id` | ObjectId | Reference to projects — unique per project |
| `total_count` | Number | Total CRs logged |
| `absorbed_count` | Number | Absorbed CR count |
| `absorbed_hours` | Number | Absorbed hours |
| `converted_count` | Number | Converted CR count |
| `converted_hours` | Number | Converted hours |
| `pending_count` | Number | Pending CR count |
| `pending_hours` | Number | Pending hours at risk |
| `removed_count` | Number | Removed CR count |
| `last_synced_at` | Date | Last successful parse timestamp |
| `sync_status` | Enum | `ok` / `file_not_found` / `parse_error` / `not_configured` |

---

### alerts
| Field | Type | Notes |
|-------|------|-------|
| `_id` | ObjectId | Primary key |
| `org_id` | String | Tenant identifier — required, indexed |
| `project_id` | ObjectId | Reference to projects |
| `type` | Enum | `MILESTONE_BEHIND` / `MILESTONE_AT_RISK` / `SPRINT_BLOCKED` / `FORENSIC_CR_PENDING` / `DOMAIN_MISMATCH` |
| `detail` | Object | Alert-specific metadata (milestone name, blocked count, etc.) |
| `sent_to` | String[] | User IDs of recipients |
| `status` | Enum | `unread` / `read` / `resolved` |
| `created_at` | Date | |

---

## Indexes

| Collection | Index Fields | Type |
|------------|-------------|------|
| All collections | `org_id` | Single |
| `clients` | `org_id + _id` | Compound |
| `projects` | `org_id + client_id` | Compound |
| `projects` | `org_id + _id` | Compound |
| `employees` | `org_id + current_assignment_project_id` | Compound |
| `employees` | `org_id + availability_status` | Compound |
| `domain_knowledge` | `org_id + employee_id` | Compound |
| `domain_knowledge` | `org_id + domain` | Compound |
| `milestones` | `org_id + project_id` | Compound |
| `sprints` | `org_id + project_id` | Compound |
| `cr_summaries` | `org_id + project_id` | Unique compound |
| `alerts` | `org_id + project_id + status` | Compound |

---

## Entity Relationships

```
Organisation
    └── Client (1:many)
            └── Project (1:many)
                    ├── Milestone (1:many)
                    ├── Sprint (1:many)
                    ├── CR Summary (1:1)
                    └── Alert (1:many)

Employee (1:org)
    └── Domain Knowledge (1:many)
    └── Project Assignment → Project (many:1)
```
