# Trackwise Onboarding Prompt — for the PM of an external project

**Purpose:** Drop this into a Claude session in the target project's repo (PM role activated). Reading it should give that PM everything needed to (a) create the Sprint Health scaffold in their repo and (b) backfill it from artifacts they already have access to.

**Audience:** The PM role on an external project being onboarded to Trackwise Sprint Health tracking. NOT Claude inside `program-management-tool`.

**Usage:** Copy everything between the `===` lines below and paste it into the target project's Claude session.

---

```
========================== BEGIN HANDOFF ==========================

# Onboarding: Trackwise Sprint Health Tracking for This Project

You are the PM for this project. Trackwise (a separate program-management tool maintained
in another repo) is going to start tracking this project's sprint health. This document
tells you exactly what to set up in this repo and how to keep it updated.

Read this whole document before acting. Then do Step 1, do Step 2 yourself from the
repos you have access to, and proceed.

---

## What Trackwise needs from this project

Trackwise tracks sprint health for every project it manages by reading **one Markdown
file** in that project's own repo. The file has a fixed structure — four tables —
that mirrors the future Trackwise DB. Trackwise's ingestion job parses the file and
projects it into the DB; the Trackwise dashboard reads the DB.

Key principles (these are locked across all Trackwise-tracked projects — do NOT modify):

1. **Same shape everywhere.** Same four tables, same columns, same column order, same
   protocol header. No per-project variations. If this project needs something the
   columns don't carry, that's a Trackwise schema change for all projects — not a local
   tweak.
2. **Markdown rows map 1:1 to future DB rows.** The columns in each table are the fields
   in the future Mongo collection.
3. **The file is the source of truth.** The Trackwise DB is a downstream projection of
   this file. The Trackwise UI is read-only over the DB. Edits happen here, in chat,
   committed to this repo.
4. **Chat-driven updates.** When the PM (or active role) states a sprint/story/bug
   event during a Claude session ("Start Sprint 3", "ST-012 done", "BUG-005 logged
   against ST-009, severity high"), Claude edits this file and commits to the repo.

---

## Step 1 — Create the file (one-time setup)

Create exactly this file at the root of this repo:

    trackwise/sprint-health.md

Folder name: `trackwise` (lowercase, no leading dot). One file inside it:
`sprint-health.md`. Nothing else in that folder for now.

**File contents — copy verbatim** (the four tables start empty; the protocol section
and field reference are part of the canonical scaffold and must be present so any
future Claude session in this repo follows the same rules):

----- BEGIN trackwise/sprint-health.md -----

# <PROJECT NAME> — Sprint Health

Source of truth for Sprint Health data on this project. Updated by Claude during chat
sessions when sprint, story, or bug events are stated. Trackwise ingests this file
into the DB; the DB is a projection of this file.

**Project ID:** <project-id-slug>
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

Use `—` for empty / null values. Dates only (no time). Always commit the change to the
repo so Trackwise can ingest it.

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

----- END trackwise/sprint-health.md -----

Replace `<PROJECT NAME>` with this project's display name and `<project-id-slug>`
with a lowercase-hyphenated identifier. Leave every other character as-is — do NOT
add columns, remove columns, rename columns, or change the protocol table.

Commit this file in its own commit:

    chore(trackwise): add Sprint Health tracking scaffold

---

## Step 2 — Backfill current state from the repos you have access to

You are the PM on this project. You have access to every artifact this project uses
to track work — sprint plans, story files, epic files, bug logs, git history, issue
tracker, whatever convention this project follows. **Retrieve the backfill data
yourself.** Do not ask the user for sprint dates, story status, or bug counts that
you can discover from the repos.

Populate all four tables to reflect history through to today:

1. **Sprints** — every sprint that has existed. For each: `sprint_id`, `name`,
   `status` (`completed` for past sprints, `in_progress` for the current one),
   `start_date`, `end_date` (`—` for the current in-flight sprint).
2. **Epics** — every epic that had work in any sprint. One row per (epic, sprint)
   pair: an epic spanning Sprint 1 and Sprint 2 produces two rows.
3. **Stories** — every story that has touched any sprint. Past-sprint stories most
   likely `status = done` (set `completed_on`). Current-sprint stories: `planned` /
   `in_progress` / `in_test` / `done` depending on actual state. Stories that started
   in one sprint and finished in a later one: set `carried_to_sprint` on the original
   row and add a fresh row in the receiving sprint with `carried_from_sprint` set.
4. **Bugs** — every bug logged in any sprint, with current `status`, `severity`,
   `logged_on`, and `resolved_on` if applicable. Link to `related_story` where the
   bug was raised against a specific story.

Use `—` for any field that genuinely doesn't apply (not for fields you couldn't be
bothered to look up). If a piece of data is genuinely unrecoverable from the repos,
record it as `—` and flag the gap in your reply to the user — don't invent it.

Commit the backfill in a separate commit from Step 1:

    chore(trackwise): backfill historical and current sprint state

---

## Step 3 — Ongoing updates

From now on, when the user states a sprint/story/bug event in any Claude session in
this repo, follow the protocol table at the top of `trackwise/sprint-health.md`,
edit the file, and commit + push. That's it.

Do NOT:
- Add new tables or columns
- Track money, hours, or velocity points (count-based only for v1.0)
- Treat the file as anything other than the source of truth — there's no other
  database to update

---

## What to report back to the user

After Step 1 + Step 2 are committed, reply with:

1. A one-line confirmation that `trackwise/sprint-health.md` is in place and
   backfilled through today.
2. The row counts you wrote (e.g., "3 sprints, 7 epics, 42 stories, 11 bugs").
3. Any gaps you couldn't fill from the repos (fields left as `—` that the user
   may want to provide).

=========================== END HANDOFF ===========================
```

---

## Maintenance notes

- **Canonical scaffold lives at** [projects/trackwise/sprint-health.md](../trackwise/sprint-health.md). If that file's columns or protocol change, update the `BEGIN trackwise/sprint-health.md` / `END trackwise/sprint-health.md` block in this template to match. Both must stay in lockstep — drift here means new projects get an out-of-date scaffold.
- **Folder convention:** this template prescribes `trackwise/` (no leading dot) per PM lock 2026-05-18. AI Code Factory (Session 11) was onboarded with `.trackwise/` before this lock — flagged for future alignment.
- **Schema version** in the scaffold is `1`. If the four-table shape changes (column add/remove/rename), bump the version in both the canonical file and this template, and document the migration in EP-10.
