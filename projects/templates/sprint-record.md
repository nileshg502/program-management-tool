# Sprint Record — [Project Name] — Sprint [N]

**Project Code:** [CODE]
**Sprint:** [Number]
**Period:** [Start date] → [End date]
**Sprint Goal:** [One line]

---

## Sprint Baseline (Locked at Sprint Start)

The sprint baseline is the Acceptance Criteria of all committed stories. ACs must be verified against the contract baseline before dev starts.

| Story | AC Verified Against Contract Baseline? | Notes |
|-------|---------------------------------------|-------|
| [Story-001] | ✅ / ❌ Flag | |
| [Story-002] | ✅ / ❌ Flag | |

**Carry-overs from previous sprint:**

| Story | Scope Changed? | Notes |
|-------|---------------|-------|
| [Story-XXX] | No change / Changed — [what changed] | |

> If any AC fails verification → run Forensic before dev starts on that story.

---

## Commitment vs Delivery

| Story | Committed | Delivered | Carry-over | Notes |
|-------|-----------|-----------|------------|-------|
| | ✅ / ❌ | ✅ / ❌ | ✅ / ❌ | |

**Summary:**
- Committed: [n] stories
- Delivered: [n] stories
- Carry-over: [n] stories — Reason: [brief reason]

---

## Effort Variance (Internal — Not Client Facing)

Track hours contracted vs hours actually spent per story. This is not drift — it is internal efficiency tracking.

| Story | Contracted Hours | Actual Hours | Variance | Reason if Over |
|-------|-----------------|-------------|----------|---------------|
| | | | | |

**Sprint Total:**
- Contracted: [x] hours
- Actual: [x] hours
- Variance: [+/- x] hours

> Effort overrun within AC scope = effort variance, not drift. Track here, not in Drift Log.
> Persistent overrun across sprints = flag as budget risk on fixed budget projects.

---

## QA Status

| Story | In Testing | Passed | Failed | Rework Required |
|-------|-----------|--------|--------|----------------|
| | | | | |

> Bug fixes and rework against existing ACs = delivery obligation, not drift. Do not log in Drift Log.

---

## Rework Log

| # | Item | AC Reference | Effort to Fix | Status |
|---|------|-------------|--------------|--------|
| | | | | |

---

## Drift Items This Sprint

Items caught via daily drift check — genuinely outside contracted scope AND not covered anywhere else in project plan.

| # | Item | Caught At | Source | Cross-Check Result | Tag | Decision |
|---|------|----------|--------|-------------------|-----|---------|
| | | Daily check / Forensic | Client request / Claude-generated / Team initiative | Not in any Epic/Story/Sprint | D / CR-Pending / CR-Converted | |

> Before logging: confirm item is not covered in another Epic, Story, or future Sprint.
> If covered elsewhere → do not log here.

---

## Sprint Pull-Forwards

Work that belongs to a future sprint but was done in this sprint. Not drift — but a sprint variance.

| # | Item | Belongs To | Reason Done Early | PM Decision |
|---|------|-----------|------------------|------------|
| | | Sprint [N] / Story [X] | | Allow / Stop / Monitor |

---

## Blockers This Sprint

| Blocker | Raised | Resolved | Days Open | Resolution |
|---------|--------|----------|-----------|------------|
| | | | | |

---

## Forensic Checks This Sprint

| Trigger | Run? | Findings |
|---------|------|---------|
| New Epics/Stories created | ✅ / ❌ / N/A | |
| ACs verified at sprint start | ✅ / ❌ | |
| Code development completed | ✅ / ❌ / N/A | |
| Client CR received | ✅ / ❌ / N/A | |

---

## Milestone Reconciliation (Sprint End)

All CR-Pending items from this sprint resolved below:

| Drift Item | Final Decision | Decision Owner |
|-----------|---------------|---------------|
| | Absorb / Bundle / Convert | |

**Three Totals This Sprint:**
- Goodwill Investment (Absorbed): [x] hours
- Deferred CR Pipeline (Bundled): [x] hours
- Converted CR Revenue: [x] hours

---

## Notes

[Any other relevant observations from this sprint]
