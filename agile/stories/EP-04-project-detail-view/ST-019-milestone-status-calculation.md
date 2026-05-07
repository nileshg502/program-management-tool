# ST-019 — Milestone status calculation — On Track / At Risk / Behind

**Story ID:** ST-019
**Epic:** EP-04 — Project Detail View
**Priority:** P1
**Points:** 8
**Status:** Not Started

---

## User Story

As the system, I need to automatically calculate whether each milestone is On Track, At Risk, or Behind, so that status badges are accurate without manual updates.

---

## Acceptance Criteria

- [ ] Status calculated using due date and % complete:
  - **Behind** — due date has passed and milestone is not 100% complete; OR % complete is more than 20 percentage points below expected pace
  - **At Risk** — due date is within 7 days and % complete is below expected pace; OR % complete is 10–20 points below expected pace
  - **On Track** — all other cases
- [ ] Expected pace = (days elapsed since milestone created / total milestone duration) × 100
- [ ] Status is recalculated on every GitHub sync
- [ ] Status stored on the Milestone record — not computed live on every page load
- [ ] Status transitions trigger alert events (consumed by EP-07)
- [ ] Unit tests cover all status boundary conditions

---

## Open Question

- Exact At Risk vs Behind thresholds are pending CTO decision (PRD Open Question #2) — use values above as defaults until decided

---

## Notes

- This story has the highest complexity in EP-04 — 8 points reflects the boundary logic, testing requirements, and alert event emission
- Depends on EP-08 for milestone due date and issue count data
