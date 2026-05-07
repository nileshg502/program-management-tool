# EP-02 — People Directory

**Epic ID:** EP-02
**Title:** Organisation People Directory
**PRD Reference:** Section 7.1
**Priority:** P1
**Status:** Not Started

---

## Goal

Give management a searchable view of all employees, their roles, domain knowledge, and current project assignment. Allow employees to self-declare and maintain their own domain knowledge.

---

## Scope

- Employee entity — name, role, domain knowledge, assignment
- Domain knowledge entity — domain name, years of experience, linked to employee
- Employee self-service — add, edit, remove own domain knowledge entries
- Directory view — list all employees, read-only for management
- Search and filter — by domain, role, availability

---

## Out of Scope

- Assigning employees to projects (EP-05)
- Availability/bench view (EP-05)

---

## Stories

| ID | Title | Points |
|----|-------|--------|
| ST-006 | Create Employee entity and admin CRUD | 5 |
| ST-007 | Create Domain Knowledge entity linked to Employee | 3 |
| ST-008 | Employee self-service — add and edit own domain knowledge | 5 |
| ST-009 | People directory list view for management | 5 |
| ST-010 | Search and filter directory by role, domain, availability | 5 |

**Total Points:** 23
