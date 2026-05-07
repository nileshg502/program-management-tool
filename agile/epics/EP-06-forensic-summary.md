# EP-06 — Forensic Summary Integration

**Epic ID:** EP-06
**Title:** Forensic Summary Integration
**PRD Reference:** Section 7.6, Section 6.7
**Priority:** P2
**Status:** Not Started

---

## Goal

Surface forensic health data — CR counts and hours by decision type — on both the project card (dashboard) and the project detail view. Data is read from the CR Summary entity which is populated from the forensic tracking process.

---

## Scope

- CR Summary entity — total, absorbed count/hours, converted count, pending count/hours, removed count, linked to project
- Forensic summary display on project card (one-line indicator)
- Forensic summary section on project detail view (EP-04 ST-022 depends on this)
- "Forensic tracking not initiated" state when no data exists
- CR Summary updated when PM records forensic decisions (manual entry or import from cr-tracker.md)

---

## Out of Scope

- Full CR log management in-app — forensic documents remain in the file system
- Automated parsing of cr-tracker.md files — v1.0 is manual/import only

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-029 | Create CR Summary entity linked to Project | 3 |
| ST-030 | Manual CR Summary entry/update by PM | 5 |
| ST-031 | Forensic one-line indicator on project card | 3 |
| ST-032 | "Forensic tracking not initiated" state handling | 2 |
| ST-033 | Forensic summary display on project detail view | 3 |

**Total Points:** 16
