# Projects

Each active project gets its own folder under `projects/[project-code]/` when onboarded into Trackwise.

## Per-Project Folder Structure

```
projects/
  [project-code]/
    baseline/                  ← locked at contract signing, never changes
      contract-baseline.md     ← signed SOW, module list, agreed hours, milestones
    requirements/
      prd.md                   ← PRD generated from client SOW
      module-list.md           ← BA-defined modules agreed with client
    epics/                     ← Epics generated from PRD
    stories/                   ← Stories generated from Epics
    forensic/
      baseline/                ← copy of contract baseline used by Forensic role
      runs/                    ← one folder per forensic run
      delta-document.md        ← running delta log across all runs
      last-run.md              ← most recent forensic run summary
    change-requests/           ← CRs raised for this project
    status/
      project-status.md        ← current RAG status, sprint, milestones
    sprints/                   ← sprint records for this project
```

## Templates

See `projects/templates/` for blank templates to use when onboarding a new project.
