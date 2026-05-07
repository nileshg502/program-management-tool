# ST-015 — Colour-coded status badges — Green / Amber / Red

**Story ID:** ST-015
**Epic:** EP-03 — Client & Project Dashboard
**Priority:** P1
**Points:** 2
**Status:** Not Started

---

## User Story

As a user, I need colour-coded status badges on client cards and project rows, so that I can read health at a glance without interpreting text labels.

---

## Acceptance Criteria

- [ ] Three badge states defined: On Track (Green), At Risk (Amber), Behind (Red)
- [ ] Badge is a reusable UI component — used on client card, project row, milestone list, and project detail view
- [ ] Colour values meet WCAG AA contrast ratio — not colour-only signalling (label text also present)
- [ ] Badge displays the status label as text alongside the colour: "On Track", "At Risk", "Behind"
- [ ] "No data" state uses a neutral grey badge with label "No data"

---

## Notes

- Design system / colour tokens should be defined as part of this story so all badge usages are consistent
