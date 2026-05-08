# ST-058 — CR Summary — add origin classification and source deviation link

**Story ID:** ST-058
**Epic:** EP-09 — Project Health Dashboard
**Priority:** P1
**Points:** 3
**Status:** Not Started

---

## User Story

As a PM, I need every CR to be classified by its origin (deviation, client request, internal, regulatory) and linked back to its source deviation when applicable, so that the Project Health Dashboard can show how many CRs were converted from deviations versus raised directly by the client.

---

## Acceptance Criteria

- [ ] `cr_summaries` schema extended with `origin` field — enum: `deviation`, `client_request`, `internal`, `regulatory`
- [ ] `cr_summaries` schema extended with `source_deviation_id` — nullable reference field; held as a string for now since the Deviation entity schema is not yet defined
- [ ] `source_deviation_id` must be null when `origin` is not `deviation` (validated server-side)
- [ ] `source_deviation_id` is **optional** when `origin = deviation` for v1.0 — a "required + foreign key validated" rule will be added once the Deviation entity is defined (PM-parked)
- [ ] `POST /api/v1/projects/{project_id}/crs` accepts `origin` and `source_deviation_id` in the request body (snake_case)
- [ ] `PATCH /api/v1/crs/{id}` accepts updates to `origin` and `source_deviation_id` with the same validation rule
- [ ] Existing CR records (created before this story) default to `origin = internal` on migration with a one-line migration note in the changelog
- [ ] Manage CR form (from ST-030) exposes an `origin` selector with the four options
- [ ] When `origin = deviation` is selected, the form shows a deviation picker scoped to the same project; the picker is hidden for other origins
- [ ] List endpoints (`GET /api/v1/projects/{project_id}/crs`) return `origin` and `source_deviation_id` on every record
- [ ] Aggregation helper available: count CRs grouped by origin for a given project (used by ST-059)

---

## Notes

- This story is the data-model prerequisite for the dashboard's CR origin breakdown. It does not add any dashboard UI itself — the dashboard is delivered by ST-060.
- `origin` and `source_deviation_id` are CRUD-owned by Trackwise. No GitHub source.
- The four-value enum is intentionally fixed for v1.0. If a fifth origin is needed later, it requires a schema change and PM approval — adding free-text would defeat the purpose of the classification.
- Per ADR-008 (pending), all CR data including origin is served from the Trackwise database. The frontend must not call GitHub or any other external system to derive origin.
- The Deviation entity is parked pending PM structure definition (see EP-09 "Blocked-by — structure definition"). `source_deviation_id` is therefore a soft reference for v1.0 — strict foreign-key validation is added in a follow-up story once the Deviation entity ships.
