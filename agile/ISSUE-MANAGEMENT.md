# Issue Management — GitHub Issues Workflow

## Three-Level Hierarchy

```
Epic Issue → Story Issue → Sub-task Issue
```

## Labels System

```
# Type
epic, story, sub-task, bug, enhancement, documentation

# Area
backend, frontend, mobile, database, devops, testing

# Priority
priority-high, priority-medium, priority-low

# Story Points
1-point, 2-points, 3-points, 5-points, 8-points, 13-points

# Status (optional)
status-backlog, status-in-progress, status-in-review, status-in-test, status-done, status-blocked

# Epic (one per epic)
epic-001-name, epic-002-name, ...
```

## Issue Workflow

```
Backlog → In Progress → In Review → In Test → Done
```

| Transition | Who | When |
|------------|-----|------|
| Backlog → In Progress | Developer | Starts working |
| In Progress → In Review | Developer | Creates PR |
| In Review → In Test | Reviewer | PR approved & merged |
| In Test → Done | QA | Tests passed |

## Epic Issue Template

```
## Epic Summary
[2-3 sentence description]

## Business Value
[Who benefits and how]

## User Stories
- [ ] US-NNN: [Story title]
- [ ] US-NNN: [Story title]

## Acceptance Criteria
- [ ] [Criterion]
```

## Story Issue Template

```
## User Story
As a [role], I want [action], so that [benefit].

## Acceptance Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]

## Story Points
[N points]

## Epic
Closes #[epic-issue-number]
```
