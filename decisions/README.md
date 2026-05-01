# Project Decisions

## Structure

```
decisions/
├── pm/           # Product Manager decisions
├── ba/           # Business Analyst decisions
├── architect/    # Architecture Decision Records (ADRs)
├── engineering/  # Engineering decisions
├── qa/           # QA decisions
└── devops/       # DevOps decisions
```

## Naming Convention

### Architect Decisions (ADRs)
Format: `ADR-NNN-title.md`
Example: `ADR-001-offline-first-architecture.md`

### Other Role Decisions
Format: `DEC-ROLE-NNN-title.md`
Examples:
- `DEC-PM-001-user-story-prioritization.md`
- `DEC-BA-001-database-selection.md`
- `DEC-ENG-001-api-client-library.md`

## Decision Template

Each decision file should include:
- **Date**: When the decision was made
- **Status**: Proposed | Approved | Rejected | Superseded
- **Context**: Why this decision was needed
- **Decision**: What was decided
- **Consequences**: Impact on the project
- **Alternatives Considered**: (if applicable)

## Rules

1. Save decisions only (not session summaries)
2. Place in the correct role folder
3. Use proper naming convention
4. Include all required sections
