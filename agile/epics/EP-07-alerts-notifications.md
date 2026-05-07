# EP-07 — Alerts & Notifications

**Epic ID:** EP-07
**Title:** Alerts and Notifications
**PRD Reference:** Section 7.7
**Priority:** P2
**Status:** Not Started

---

## Goal

Automatically surface in-app alerts when defined thresholds are crossed, so management and PMs are informed without manual checking. All 5 alert trigger types from the PRD must be covered.

---

## Scope

**Alert triggers (all 5 from PRD):**
1. Milestone moves from On Track to Behind → CTO + PM
2. Milestone moves from On Track to At Risk → PM
3. Sprint has more than 3 blocked stories → PM
4. A forensic CR has been Pending for more than 1 sprint → PM
5. A team member has no domain knowledge matching the project → PM

**Alert entity** — type, trigger, project, sent to, timestamp
**In-app notification panel** — unread count badge, notification list
**Alert history** — per-project list of all past alerts

---

## Out of Scope

- Email or SMS notifications (v1.0 is in-app only)
- Configurable alert thresholds per project (v1.0 uses fixed thresholds from PRD)

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-034 | Create Alert entity and notification store | 3 |
| ST-035 | Alert trigger — milestone status change to Behind | 5 |
| ST-036 | Alert trigger — milestone status change to At Risk | 3 |
| ST-037 | Alert trigger — sprint blocked stories exceed 3 | 5 |
| ST-038 | Alert trigger — forensic CR Pending for more than 1 sprint | 5 |
| ST-039 | Alert trigger — team domain knowledge mismatch on project | 3 |
| ST-040 | In-app notification panel with unread count badge | 5 |
| ST-041 | Alert history view per project | 3 |

**Total Points:** 32
