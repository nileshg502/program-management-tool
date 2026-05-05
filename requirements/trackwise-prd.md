# Product Requirements Document — Trackwise

Version       : v1.0
Status        : Draft
Created On    : 2026-05-05
Created By    : PM
Last Updated  : 2026-05-05

---

## 1. Overview

Trackwise is a program management tool for software delivery organisations. It gives management a single place to see client and project health, team composition, domain expertise, and scope deviation — without manually checking GitHub or chasing status updates.

The tool reads from GitHub at milestone and sprint level. It does not go more micro than sprint.

---

## 2. Problem Statement

Management currently has no single view of:
- Which clients have active projects and whether those projects are on track
- Who in the organisation has what domain knowledge and where they are allocated
- Whether scope deviation is accumulating silently across projects

Status updates are manual, scattered, and delayed. By the time management sees a problem it is already late.

---

## 3. Goals

- Give CTO/CEO a real-time portfolio health view across all clients and projects
- Give PM a project-level view of milestone and sprint health
- Surface team domain knowledge at project and organisation level
- Surface forensic health — scope deviation risk — alongside delivery risk
- Eliminate manual status reporting by reading directly from GitHub

---

## 4. Non-Goals

- This tool does not replace GitHub — it reads from it
- This tool does not go below sprint level — no individual commit or PR tracking
- This tool does not manage timesheets or payroll
- This tool does not send client-facing communications

---

## 5. Users

| User | Role | What They Need |
|------|------|---------------|
| CTO / CEO | Executive | Portfolio health — all clients, all projects, one screen |
| PM | Project Manager | Project health — milestones, sprints, team, forensic summary |

---

## 6. Core Concepts

### 6.1 Organisation
The company using Trackwise. Has employees, clients, and projects.

### 6.2 Client
A client the organisation is delivering work for. A client can have one or more active projects.

### 6.3 Project
A software delivery engagement for a client. Has a team, milestones, sprints, and a forensic tracking record.

### 6.4 Employee
A person in the organisation. Has a role, domain knowledge, years of experience per domain, and a current project assignment.

### 6.5 Domain Knowledge
Industry domains an employee has experience in — e.g. FinTech, HealthCare, eCommerce, Logistics. Self-declared by the employee. Management can view but not override.

### 6.6 Milestone Status
Calculated automatically from GitHub data:
- **On Track** — milestone due date is in the future and % complete is on pace
- **At Risk** — milestone due date is approaching and % complete is behind pace
- **Behind** — milestone due date has passed or stories significantly incomplete

### 6.7 Forensic Health
A summary of scope deviation activity on a project — total CRs logged, pending decisions, absorbed hours, converted CRs.

---

## 7. Features

### 7.1 Organisation People Directory

**Description:**
A directory of all employees in the organisation.

**Each employee record contains:**
- Name
- Role — Engineer / QA / BA / Architect / DevOps / PM
- Domain knowledge — list of domains with years of experience per domain
- Current project assignment — or "Available" if unassigned

**Behaviour:**
- Employee adds and updates their own domain knowledge
- Management views the directory — read only
- Directory is searchable by domain, role, and availability

---

### 7.2 Client and Project Dashboard

**Description:**
Top level view for CTO/CEO showing all clients and their active projects.

**Each client card shows:**
- Client name
- Number of active projects
- Overall status — worst status across all projects

**Each project under a client shows:**
- Project name
- Current milestone name and due date
- Milestone status — On Track / At Risk / Behind
- Project team size

**Behaviour:**
- Status colour coded — Green (On Track) / Amber (At Risk) / Red (Behind)
- Clicking a project opens the Project Detail view

---

### 7.3 Project Detail View

**Description:**
Full project health view for PM and management.

**Sections:**

**Overview**
- Client name
- Project name and code
- Delivery type — Fixed Scope / Fixed Budget
- Go-Live date
- Current milestone

**Milestone Health**
- List of all milestones
- For each: name, due date, % complete, status
- Data pulled from GitHub

**Sprint Health**
- Current sprint name and number
- Stories committed vs completed
- Stories in progress
- Blocked stories — any GitHub issue labelled "blocked"
- Data pulled from GitHub

**Team**
- List of people assigned to this project
- For each: name, role, domain knowledge relevant to this project

**Forensic Summary**
- Total CRs logged
- Absorbed — count and hours
- Converted — count
- Pending — count and hours at risk
- Removed — count

---

### 7.4 Team Assignment per Project

**Description:**
Management can assign employees to projects and see team composition at a glance.

**Behaviour:**
- Management assigns / removes people from a project
- Each assignment shows role and relevant domain knowledge
- If a project requires domain knowledge no team member has — tool flags a mismatch

---

### 7.5 Availability and Bench View

**Description:**
Organisation-wide view of employee allocation.

**Each employee shows:**
- Name and role
- Status — Assigned / Available / Partially Available
- Current project — if assigned

**Behaviour:**
- When scoping a new project, management filters by domain and availability
- Partially Available — assigned to a project but under-utilised flag set by PM

---

### 7.6 Forensic Summary per Project

**Description:**
One-line forensic health indicator on every project card and project detail view.

**Shows:**
- Total CRs logged
- Pending decisions — count and hours
- Absorbed — count and hours
- Converted CRs — count

**Behaviour:**
- Data comes from `cr-tracker.md` in the project forensic folder
- If no forensic tracking has started — shows "Forensic tracking not initiated"

---

### 7.7 Alerts and Notifications

**Description:**
Automatic alerts sent to the relevant user when a threshold is crossed.

**Alert triggers:**

| Trigger | Sent To |
|---------|---------|
| Milestone moves from On Track to Behind | CTO, PM |
| Milestone moves from On Track to At Risk | PM |
| Sprint has more than 3 blocked stories | PM |
| A forensic CR has been Pending for more than 1 sprint | PM |
| A team member has no domain knowledge matching the project | PM |

**Behaviour:**
- Alerts shown in-app as notifications
- Alert history available per project

---

## 8. GitHub Integration

**What the tool reads from GitHub:**
- Milestones — name, due date, open vs closed issues count
- Sprint labels — stories tagged with sprint label, open vs closed status
- Blocked label — issues tagged "blocked"

**What the tool does NOT read:**
- Individual commits
- Pull requests
- Code content
- Comments

**Sync frequency:**
- GitHub data refreshed every 15 minutes automatically
- Manual refresh available per project

---

## 9. RBAC — Role Based Access Control

| Feature | CTO / CEO | PM |
|---------|-----------|-----|
| View all clients and projects | Yes | Own projects only |
| View portfolio dashboard | Yes | No |
| View project detail | Yes | Yes |
| Assign team to project | Yes | Yes |
| View people directory | Yes | Yes |
| Edit own domain knowledge | No | No — employee does this |
| View forensic summary | Yes | Yes |
| View alerts | Yes | Yes — own projects only |

---

## 10. Data Model — High Level

| Entity | Key Fields |
|--------|-----------|
| Organisation | Name, logo |
| Client | Name, industry, active projects |
| Project | Name, code, client, delivery type, go-live, GitHub repo, team |
| Employee | Name, role, domain knowledge, current assignment |
| Domain Knowledge | Domain name, years of experience, employee |
| Milestone | Name, due date, % complete, status, project |
| Sprint | Number, name, stories committed, stories complete, blocked count, project |
| CR Summary | Total, absorbed, converted, pending, removed, project |
| Alert | Type, trigger, project, sent to, timestamp |

---

## 11. Out of Scope for v1.0

- Timesheet and hour tracking
- Client portal — client-facing view
- Budget and invoice management
- Mobile app
- Integration with tools other than GitHub

---

## 12. Open Questions

| # | Question | Owner | Status |
|---|----------|-------|--------|
| 1 | Which GitHub label convention is used for sprints? | PM | Open |
| 2 | What is the exact threshold for At Risk vs Behind? | CTO | Open |
| 3 | How is Partially Available defined — hours or PM flag? | PM | Open |

---

## 13. Version History

| Version | Date | Change | Author |
|---------|------|--------|--------|
| v1.0 | 2026-05-05 | Initial draft | PM |
