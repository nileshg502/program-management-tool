# Trackwise — Sprint Health

Source of truth for Sprint Health data on the Trackwise project. Updated by Claude during chat sessions when sprint, story, or bug events are stated. Trackwise ingests this file into the DB; the DB is a projection of this file.

**Project ID:** trackwise
**Schema version:** 1

---

## Update protocol (for Claude)

When the active role makes a statement during a chat session, update the relevant table:

| Statement | Action |
|---|---|
| "Start Sprint N" / "Sprint N implementation started" | Add row to Sprints with `status = in_progress`, `start_date = today` |
| "Sprint N is complete" / "End Sprint N" | Update Sprints row to `status = completed`, `end_date = today` |
| "Add ST-XXX to Sprint N, points X, epic EP-YY, assignee NAME" | Add row to Stories with `status = planned`, `added_to_sprint_on = today` |
| "ST-XXX in progress / in test / done" | Update Stories row `status`; set `completed_on = today` on done |
| "ST-XXX carried to Sprint N+1" | Set `carried_to_sprint` on old row; add new row in new sprint with `carried_from_sprint` set |
| "Add EP-YY to Sprint N, points X" | Add row to Epics |
| "BUG-XXX logged against ST-YY, severity S" | Add row to Bugs with `status = open`, `logged_on = today` |
| "BUG-XXX resolved" / "BUG-XXX won't fix" | Update Bugs row `status` and `resolved_on = today` |

Use `—` for empty / null values. Dates only (no time). Always commit the change to the repo so Trackwise can ingest it.

---

## Sprints

| sprint_id | name | status | start_date | end_date |
|---|---|---|---|---|

*No sprints recorded yet.*

---

## Epics

| epic_id | title | sprint_id | status | points_planned | points_completed |
|---|---|---|---|---|---|

*No epics recorded yet.*

---

## Stories

| story_id | title | sprint_id | epic_id | status | points | assignee | added_to_sprint_on | completed_on | carried_from_sprint | carried_to_sprint |
|---|---|---|---|---|---|---|---|---|---|---|

*No stories recorded yet.*

---

## Bugs

| bug_id | title | sprint_id | severity | status | logged_on | resolved_on | related_story |
|---|---|---|---|---|---|---|---|

*No bugs recorded yet.*

---

## Field reference

### Sprints
- `sprint_id` — text, lowercase with hyphen (e.g., `sprint-1`)
- `name` — text (e.g., `Sprint 1`)
- `status` — enum: `planned` / `in_progress` / `completed`
- `start_date`, `end_date` — date (`YYYY-MM-DD`) or `—`

### Epics
- `epic_id` — text (e.g., `EP-01`)
- `title` — text
- `sprint_id` — FK to Sprints; one epic can appear in multiple sprints (one row per sprint)
- `status` — enum: `not_started` / `in_progress` / `completed`
- `points_planned`, `points_completed` — number

### Stories
- `story_id` — text (e.g., `ST-001`)
- `title` — text
- `sprint_id` — FK to Sprints
- `epic_id` — FK to Epics
- `status` — enum: `planned` / `in_progress` / `in_test` / `done` / `carried_over` / `dropped`
- `points` — number
- `assignee` — text (username)
- `added_to_sprint_on`, `completed_on` — date or `—`
- `carried_from_sprint`, `carried_to_sprint` — FK to Sprints or `—`

### Bugs
- `bug_id` — text (e.g., `BUG-001`)
- `title` — text
- `sprint_id` — FK to Sprints
- `severity` — enum: `low` / `medium` / `high` / `critical`
- `status` — enum: `open` / `in_progress` / `resolved` / `closed` / `wont_fix`
- `logged_on`, `resolved_on` — date or `—`
- `related_story` — FK to Stories or `—`
