# Agile Workflow

**Methodology**: Scrum (2-week sprints)
**Team Size**: TBD

---

## Story Point Estimation (Fibonacci)

| Points | Complexity | Effort | Examples |
|--------|------------|--------|----------|
| **1** | Trivial | < 2 hours | Config change, simple text update |
| **2** | Very Simple | 2-4 hours | Add validation, simple CRUD |
| **3** | Simple | 4-8 hours | Basic UI component, simple service |
| **5** | Medium | 1-2 days | Standard feature, moderate complexity |
| **8** | Complex | 2-3 days | Multi-component feature, integration |
| **13** | Very Complex | 3-5 days | Large feature, complex logic |
| **21** | Too Large | >5 days | **BREAK DOWN INTO SMALLER STORIES** |

Rule: Stories >13 points must be split.

---

## Definition of Done (Story Level)

- [ ] Code implemented and peer-reviewed
- [ ] All acceptance criteria met
- [ ] Unit tests written and passing (80%+ coverage)
- [ ] Integration tests passing
- [ ] Code merged to main branch
- [ ] Documentation updated
- [ ] QA sign-off received
- [ ] Deployed to staging
- [ ] No critical or high-priority bugs

---

## Ceremonies

### Sprint Planning (Bi-weekly) — 2 hours
- Review and commit to sprint backlog
- Clarify acceptance criteria
- Assign stories to team members

### Daily Standup (Daily) — 15 minutes
- What did I do yesterday?
- What will I do today?
- Any blockers?

### Sprint Review (Bi-weekly) — 1 hour
- Demo completed stories to stakeholders
- Gather feedback
- Update product backlog

### Sprint Retrospective (Bi-weekly) — 1 hour
- What went well?
- What needs improvement?
- Action items for next sprint

### Backlog Grooming (Weekly) — 1 hour
- Refine and estimate upcoming stories
- Break down epics into stories
- Reprioritize as needed
