# Trackwise — PM Process Document

**Organization:** Trackwise
**Author:** Program Manager
**Status:** Draft v1.0 — Pending CTO Review
**Date:** 2026-05-01

---

## Purpose

This document defines the standard processes a Program Manager follows to manage multiple concurrent software delivery projects. It ensures consistent visibility, proactive communication, and structured delivery across all projects — so that at any given moment, the PM can provide a confident, accurate update to leadership.

---

## Scope

This process applies to all active software delivery projects managed under Trackwise. It covers:
- Fixed scope projects (timeline and effort are the variable)
- Fixed budget projects (scope is the variable, budget is the constraint)

---

## 1. Project Intake & Kickoff Process

### When
Every time a new project is initiated.

### Steps
1. **Receive project brief** from client or internal stakeholder
2. **Identify project type** — fixed scope or fixed budget
3. **Define delivery milestones** — agree on major checkpoints with the client
4. **Confirm team** — who is developing, who is doing QA, any external dependencies
5. **Set up project tracking** — create project entry in tracking tool, assign a RAG status (default: Green)
6. **Document baseline** — record agreed scope, timeline, and budget at kickoff
7. **Kickoff communication** — notify team of project start, share milestones and responsibilities

### Output
- Project baseline documented (scope, timeline, budget)
- Milestones agreed and recorded
- Team aware and aligned

---

## 2. Sprint Tracking Process

### When
Every sprint (typically every 1–2 weeks).

### Steps
1. **At sprint start:**
   - Confirm sprint goal with development team
   - Note what stories/tasks are committed for this sprint
   - Record sprint start date and expected end date

2. **During sprint:**
   - Check with dev team on blockers (daily or as needed)
   - Check with QA on what is ready for testing, in testing, or ready to ship
   - Flag any slippage immediately — do not wait for sprint end

3. **At sprint end:**
   - Record what was completed vs committed (velocity)
   - Note any carry-over items and reason
   - Update project status (RAG) based on sprint outcome
   - Confirm next sprint plan is ready

### Output
- Sprint completion record (committed vs delivered)
- Carry-over log with reasons
- Updated RAG status

---

## 3. Milestone & Delivery Management Process

### When
Ongoing — reviewed every sprint and before any leadership update.

### Steps
1. **At project kickoff** — define 4–6 key delivery milestones with dates
2. **Each sprint** — check if the project is on track to hit the next milestone
3. **If at risk** — raise a flag immediately, do not wait for the milestone to be missed
4. **If missed** — record the revised date, root cause, and impact on subsequent milestones
5. **Client communication** — any milestone change must be communicated to the client before it is missed, not after

### Milestone Types to Track
| Milestone Type | Description |
|---------------|-------------|
| Scope Freeze | No new features after this date |
| Development Complete | All features built and unit tested |
| QA Sign-off | Testing complete, ready to ship |
| Client UAT | Client acceptance testing window |
| Go-Live | Production deployment |
| Post-Go-Live Review | Stability check after launch |

### Output
- Milestone tracker updated every sprint
- Any changes documented with root cause
- Client informed before any milestone slips

---

## 4. Blocker & Risk Management Process

### When
Continuously — blockers raised as they occur, risks reviewed weekly.

### Blocker Process
1. Dev team or PM identifies a blocker
2. PM logs the blocker immediately — what it is, who owns it, date raised
3. PM checks daily on open blockers — is it resolved or escalating?
4. If blocker is open for more than **2 days**, PM escalates
5. Once resolved, log resolution date and what unblocked it

### Risk Process
1. At kickoff, identify top 3 risks for the project
2. Each week, review: are risks still valid? Have new risks emerged?
3. For each risk, record:
   - **Likelihood** — High / Medium / Low
   - **Impact** — High / Medium / Low
   - **Mitigation** — what is being done to reduce it
   - **Owner** — who is responsible for managing it
4. If a risk becomes a blocker, move it to the blocker log

### Escalation Criteria
Escalate to CTO/CEO if:
- A blocker has been open more than 3 days with no resolution path
- A milestone will be missed by more than 1 sprint
- Budget overrun is projected
- Client is escalating

### Output
- Active blocker log (owner + days open)
- Risk register (reviewed weekly)
- Escalation record when raised

---

## 5. Budget & Effort Tracking Process

### When
Weekly for fixed budget projects. Monthly for fixed scope projects.

### Fixed Budget Projects
1. Record the total approved budget at kickoff
2. Each sprint, estimate effort spent (hours or story points)
3. Track: **budget used vs budget remaining vs work remaining**
4. If burn rate suggests budget will be exhausted before work is done — flag immediately
5. Scope change decisions must factor in remaining budget
6. Any scope addition must be evaluated against budget impact first

### Fixed Scope Projects
1. Record baseline effort estimate at kickoff
2. Track actual effort vs estimate each sprint
3. If actual is running significantly over estimate — flag as a risk
4. Document reasons for overrun (scope creep, rework, complexity)

### Output
- Budget burn summary per sprint (fixed budget projects)
- Effort variance log (fixed scope projects)
- Early warning flag if overrun projected

---

## 6. Scope & Change Management Process

### When
Any time a change is requested — by client, leadership, or team.

### Steps
1. **Change request received** — from any source (client, team, leadership)
2. **PM logs the request** — what is being asked, who requested it, date received
3. **Impact assessment** — PM works with dev team to assess:
   - Effort required
   - Impact on timeline
   - Impact on budget (especially fixed budget projects)
4. **PM decision or escalation:**
   - Minor changes within existing scope — PM approves
   - Changes impacting timeline or budget — PM escalates to CTO/client for approval
5. **Outcome recorded** — approved, rejected, or deferred with reason
6. **Baseline updated** if change is approved

### Output
- Change request log (all requests, not just approved ones)
- Updated project baseline when changes are approved
- Client informed of impact before change is implemented

---

## 7. Leadership Reporting Process

### Audience
CTO and CEO.

### Frequency
Regular updates — at minimum weekly, plus on-demand when asked.

### What CTO/CEO Want to Know
- Is every project on track?
- What is the one thing that needs their attention right now?
- Are we going to deliver on time and within budget?
- Any client escalations?

### Weekly Update Format (per project)
```
Project: [Name]
Type: Fixed Scope / Fixed Budget
Status: 🟢 Green / 🟡 Amber / 🔴 Red

Sprint: [Current sprint number] — [On Track / At Risk / Delayed]
Next Milestone: [Milestone name] — [Date] — [Confidence: High / Medium / Low]
Budget: [On Track / At Risk / Overrun] (fixed budget projects only)
Top Blocker: [One line — or "None"]
Ask from Leadership: [One line — or "None"]
```

### RAG Status Definitions
| Status | Meaning |
|--------|---------|
| 🟢 Green | On track — no significant issues |
| 🟡 Amber | At risk — issues exist but being managed, leadership aware |
| 🔴 Red | Off track — milestone at risk, budget overrun, or client escalation — needs leadership action |

### On-Demand Update Rule
At any point leadership asks for an update, the PM must be able to provide:
- Current RAG status for every project
- Next milestone for every project
- Top blocker for every project (if any)
- Any open asks requiring leadership decision

This means the PM process document and trackers must always be **current — never more than 1 sprint behind.**

### Output
- Weekly status update shared with CTO/CEO
- On-demand readiness at all times

---

## 8. QA & Delivery Handoff Process

### When
At end of each sprint and at each major milestone.

### Steps
1. **Dev team signals QA** — feature/sprint work is ready for testing
2. **QA begins testing** — PM tracks: what is in testing, what has passed, what has failed
3. **QA reports to PM:**
   - What is ready to ship
   - What needs rework (with severity)
   - Estimated rework time
4. **PM updates sprint status** based on QA outcome
5. **Rework tracking:**
   - Log every rework item — what failed, why, how long to fix
   - Persistent rework on the same area = flag as a quality risk
6. **Delivery confirmation:**
   - PM confirms with QA before marking anything as delivered
   - PM communicates delivery to client

### Output
- QA status per sprint (in testing / passed / failed / rework)
- Rework log (pattern tracking)
- Delivery confirmation record

---

## 9. Communication Process

### Internal (Team)
| Trigger | Action |
|---------|--------|
| Blocker raised | PM informed same day |
| Sprint end | PM reviews with team |
| Milestone approaching (1 week out) | PM checks confidence level with team |
| Rework identified | PM logs and tracks |

### External (Client)
| Trigger | Action |
|---------|--------|
| Milestone delivered | PM notifies client |
| Milestone at risk | PM informs client before it is missed |
| Scope change request | PM responds within 2 business days with impact assessment |
| Escalation from client | PM escalates to CTO same day |

### Leadership (CTO/CEO)
| Trigger | Action |
|---------|--------|
| Weekly cadence | PM sends status update for all projects |
| Red status on any project | PM informs leadership immediately |
| Escalation needed | PM flags with context and a recommended action |
| Leadership asks on-demand | PM responds within the hour |

---

## 10. Process Health & Review

### Monthly
- PM reviews all projects: are processes being followed?
- Any recurring blockers or risks that point to a process gap?
- Is the RAG status accurate — or is Amber being used to avoid Red?

### Quarterly
- Full process review with CTO
- Are the milestones being set realistically?
- Is the change management process working?
- Does the tooling support the process or create friction?

---

## Summary — PM Weekly Checklist

| # | Action | Frequency |
|---|--------|-----------|
| 1 | Check sprint status for all projects | Daily |
| 2 | Check open blockers — any unresolved >2 days? | Daily |
| 3 | Check QA status — what is in testing, what passed, what needs rework | Every sprint |
| 4 | Update milestone tracker — any slippage? | Every sprint |
| 5 | Review risks — any new risks, any escalating? | Weekly |
| 6 | Update RAG status for all projects | Weekly |
| 7 | Send leadership update (CTO/CEO) | Weekly |
| 8 | Check budget burn (fixed budget projects) | Weekly |
| 9 | Review change request log — any pending decisions? | Weekly |
| 10 | Confirm next sprint is planned and team is aligned | Every sprint end |

---

*Document Status: Draft v1.0 — Pending CTO Review*
*Next Review: After CTO feedback*
