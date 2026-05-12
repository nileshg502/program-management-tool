# EP-10 — Sprint Health

**Epic ID:** EP-10
**Title:** Sprint Health (per-sprint + consolidated view)
**PRD Reference:** Section 12 (extension — to be added in next PRD revision)
**Priority:** P1
**Status:** Not Started
**Last Updated:** 2026-05-12 (Session 10 — PM)

---

## Goal

Give PM a per-project view of how each sprint actually went — committed vs delivered, bugs logged vs resolved, and what carried forward — plus a consolidated view across all sprints. Answers the questions: "What's in this sprint?", "What got done?", "What's leaking into the next sprint?"

---

## Data source — chat-driven markdown file (Session 10 redesign)

**Sprint Health data is NOT derived from GitHub.** Session 10 superseded the earlier GitHub-cache model.

Sprint Health is sourced from a per-project markdown file maintained by Claude during chat sessions. The file is the single source of truth; the Trackwise DB is a projection of this file.

**File path:** `projects/<project-id>/sprint-health.md` (for Trackwise itself: `projects/trackwise/sprint-health.md`)

**File contents:** Four markdown tables, each mirroring a future DB table:

1. **Sprints** — `sprint_id`, `name`, `status`, `start_date`, `end_date`
2. **Epics** — `epic_id`, `title`, `sprint_id`, `status`, `points_planned`, `points_completed`
3. **Stories** — `story_id`, `title`, `sprint_id`, `epic_id`, `status`, `points`, `assignee`, `added_to_sprint_on`, `completed_on`, `carried_from_sprint`, `carried_to_sprint`
4. **Bugs** — `bug_id`, `title`, `sprint_id`, `severity`, `status`, `logged_on`, `resolved_on`, `related_story`

**Update mechanism:** PM (or active role) states a sprint/story/bug event during a Claude session. Claude edits the markdown file, commits to repo. Trackwise ingests the file into the DB on a schedule (mechanism TBD by Architect — likely poll or commit webhook).

**Why this model:** GitHub-derived sprint data (the Session 8 ADR-008 / ADR-011 path) was over-engineered for the actual workflow. The projects Trackwise tracks do not use sprint labels in GitHub. Sprint operations happen in Claude sessions with the PM, and the file format mirrors the DB so non-technical readers can edit/read it directly.

---

## Scope

- Sprint Health section on Project Detail page (per project)
- Sprint selector dropdown — `Overall` (default, consolidated) + every sprint in the file
- Per-sprint view (when a specific sprint is selected) — four blocks:
  1. **Epics & Stories** — counts pulled from the file's Epics + Stories tables filtered by `sprint_id`, with a list of items where `carried_from_sprint` or `carried_to_sprint` is non-empty
  2. **Planned vs Completed** — counts only, for stories and bugs (status=`done` and status=`resolved`/`closed` respectively); epics shown with completion deferred (see Notes)
  3. **Bugs Logged vs Resolved** — bugs with this `sprint_id` (logged in sprint) plus bugs with non-empty `carried_from_sprint` (pulled forward); resolved count for the sprint
  4. **End-of-sprint carryover** — count of bugs, stories, and epics with non-empty `carried_to_sprint` for this sprint
- Consolidated view (`Overall` selected, default) — cross-sprint visualisation of the same four blocks; exact shape PM-deferred
- Sprint Health read model and `GET /projects/{id}/sprint-health` API
- Sprint Health UI section on Project Detail screen
- File ingestion pipeline — Trackwise reads `projects/<id>/sprint-health.md`, parses tables, upserts into DB

---

## Out of Scope

- Cross-project sprint rollup (per-project only)
- Money / hours / velocity points — count-based metrics only in v1.0
- Sprint retrospective notes or annotations
- Editing Sprint Health data from within the Trackwise UI — file is the only write path; UI is read-only
- Real-time updates — ingestion is on a schedule (Architect to define interval)
- GitHub Action / GitHub-derived sprint data — superseded by the file model

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-061 | Sprint Health file ingestion — parse `sprint-health.md` and project into DB | 8 |
| ST-062 | Sprint Health UI — per-sprint view with sprint selector dropdown | 5 |
| ST-063 | Sprint Health UI — consolidated `Overall` view across sprints | 5 |

**Total Points:** 18

---

## Dependencies

- **No longer depends on ST-044, ST-045 or ADR-011** — the file model replaces GitHub-derived sprint data
- New dependency: Sprint Health DB collection (Architect to spec — schema mirrors the four tables in `sprint-health.md`)
- New dependency: file parser + ingestion job (part of ST-061)

---

## Field definitions (mirror of DB schema)

### Sprints
- `sprint_id` — text, e.g. `sprint-1`
- `name` — text, e.g. `Sprint 1` (auto-derived from `sprint_id`)
- `status` — enum: `planned` / `in_progress` / `completed`
- `start_date`, `end_date` — date (`YYYY-MM-DD`)

### Epics
- `epic_id` — text, e.g. `EP-01`
- `title` — text
- `sprint_id` — FK to Sprints. One epic per sprint per row (epic spanning sprints → multiple rows)
- `status` — enum: `not_started` / `in_progress` / `completed`
- `points_planned`, `points_completed` — number

### Stories
- `story_id`, `title`, `sprint_id`, `epic_id`, `points`, `assignee` — as named
- `status` — enum: `planned` / `in_progress` / `in_test` / `done` / `carried_over` / `dropped`
- `added_to_sprint_on`, `completed_on` — date
- `carried_from_sprint`, `carried_to_sprint` — FK to Sprints (nullable)

### Bugs
- `bug_id`, `title`, `sprint_id`, `related_story` — as named
- `severity` — enum: `low` / `medium` / `high` / `critical`
- `status` — enum: `open` / `in_progress` / `resolved` / `closed` / `wont_fix`
- `logged_on`, `resolved_on` — date

---

## Notes

- EP-09 already has a Sprint Progress tile in its KPI block. EP-10 is the deeper view sitting underneath that tile, not a replacement
- Per-sprint epic completion in block #2 (Planned vs Completed) is count-only — epics span sprints by design, so "completed in sprint" is meaningless; PM defers epic-completion-in-sprint rules
- Consolidated `Overall` view shape is PM-deferred — ST-063 ships minimal (trend table per sprint × four blocks); revisit format after first use
- All numbers are count-based for v1.0
- Work-item vocabulary = **epics / stories / bugs**
- **EP-09 conflict to resolve:** EP-09's Project Health Dashboard also reads sprint data. The EP-09 / ST-059 design currently assumes GitHub-derived data. After EP-10's file model lands, EP-09 should read the same file (or read from the same DB projection of it) for consistency. Flag for next PM session.

---

## Architect follow-ups (Session 10 → next Architect session)

- Define Sprint Health DB collection schema (mirror of the four tables)
- Define file ingestion mechanism: parser, schedule (poll vs commit webhook), error handling on malformed file
- Supersede ADR-008's GitHub-as-source-for-sprint-data clause and ADR-011's sprint-membership cache — for Sprint Health specifically. ADR-008 / ADR-011 still hold for other GitHub-mirrored data
- Decide: does EP-09's Project Health Dashboard also read from this file, or from the DB projection of it?
