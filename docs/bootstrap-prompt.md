# Bootstrap Prompt — Trackwise Role-Based Workflow for a New Repo

**Purpose:** Paste the prompt below (everything between the `=== BEGIN PROMPT ===` and `=== END PROMPT ===` markers) into a Claude Code session opened inside an existing project repository. The target Claude will create the role files, `CLAUDE.md`, `SESSION-STATE.md`, `MEMORY.md`, and supporting folders so the new repo works the same way this one does.

**How to use:**

1. Open Claude Code in the target project's repo root.
2. Copy everything inside the prompt block below.
3. Before pasting, fill in the four placeholders at the top of the prompt:
   - `<<PROJECT_NAME>>` — e.g. *AI Code Factory*
   - `<<PROJECT_CODE>>` — short 3–4 letter code, e.g. `AICF`
   - `<<ORG>>` — GitHub org / owner, e.g. `nileshg502`
   - `<<REPO>>` — repo name as on GitHub, e.g. `ai-code-factory`
4. Paste and send. The target Claude will create every file inline — no copying needed from this repo.

> **Safety guarantee built into the prompt:** the bootstrap is **strictly additive** — it only creates new files in new top-level folders. It will not modify, rename, move, or delete any existing source file, config file, `.env`, `package.json`, lockfile, CI config, branch, or remote. If any of the target paths already exist, the target Claude will stop and ask before doing anything.

**Providing access (AWS / MongoDB / GitHub / etc.):**

Do **not** paste secrets into the bootstrap prompt itself. After bootstrap finishes, hand credentials to Claude in a follow-up message and let it write them to the right place:

- **Local-only secrets** → `.env.local` (or `.env`) at repo root. Confirm `.env*` is in `.gitignore` (the prompt's post-run check verifies this).
- **Shared/team secrets** → use the project's existing secret manager (AWS Secrets Manager, SSM Parameter Store, GitHub Actions secrets, Doppler, 1Password, etc.). Don't commit them.
- **Connection strings for documentation** → store the *shape* (e.g. `mongodb+srv://<user>:<pass>@cluster.xxx.mongodb.net/<db>`) in `architecture/` or a per-environment README, never the real values.
- **Cloud credentials (AWS)** → use named profiles via `aws configure --profile <project>` or environment variables sourced from your secret manager. Never write access keys to any tracked file.

When you're ready to grant access, tell the target Claude: *"I will share AWS / Mongo credentials — put them in `.env.local` and confirm `.gitignore` covers them."* That keeps the boundary clear.

**What gets created in the target repo:**

```
<target-repo>/
├── CLAUDE.md
├── SESSION-STATE.md
├── roles/
│   ├── PM.md
│   ├── BA.md
│   ├── ARCHITECT.md
│   ├── UX-DESIGNER.md
│   ├── ENGINEERING.md
│   ├── QA.md
│   └── DEVOPS.md
├── api-contract/
│   └── CONVENTIONS.md
├── memory/
│   └── MEMORY.md
├── requirements/         (empty — for PRDs, use cases)
├── architecture/         (empty — for system design, data models)
├── decisions/            (empty — for ADRs)
├── agile/                (empty — for epics, stories, sprints)
└── design/               (empty — for mockups, wireframes)
```

Forensic role and folder are intentionally excluded.

---

=== BEGIN PROMPT ===

You are setting up a role-based working model in this **existing** repository so future Claude Code sessions can pick up context and act as PM / BA / Architect / UX / Engineering / QA / DevOps.

**Critical safety rules — read first, follow exactly:**

1. **Strictly additive.** Only create the files and folders listed below. Do NOT modify, rename, move, delete, or stage any existing file. Do NOT touch source code, configs, `package.json`, lockfiles, `Dockerfile`, CI/CD files, infrastructure-as-code, migrations, `.env*`, secrets, or anything outside the new paths I list.
2. **No git operations beyond reads.** Do NOT run `git add`, `git commit`, `git push`, `git checkout`, `git branch`, `git stash`, `git reset`, or `git merge`. Read-only git commands (`git status`, `git rev-parse`, `git log`) are fine.
3. **No package or dependency changes.** Do NOT run `npm install`, `pip install`, `pnpm`, `yarn`, `poetry`, `pip-compile`, `cargo`, `go get`, or any installer. No `package.json` edits.
4. **No process / network actions.** Do NOT start servers, run migrations, hit databases, call cloud APIs (AWS, Mongo Atlas, etc.), or read secrets from env. Setup is filesystem-only.
5. **Pre-flight check before writing anything:**
   - Run `git status` — if the working tree is not clean, list the dirty files and ask whether to continue.
   - Check whether `CLAUDE.md`, `SESSION-STATE.md`, `roles/`, `api-contract/`, `memory/`, `requirements/`, `architecture/`, `decisions/`, `agile/`, or `design/` already exist. If any do, **stop and list them** — ask which to overwrite, skip, or merge before creating anything.
   - Check whether `.gitignore` exists and whether it covers `.env*`, `.env.local`, `node_modules/`, and OS junk (`.DS_Store`, `Thumbs.db`). If `.gitignore` is missing or doesn't cover `.env*`, flag it after the run — do not edit `.gitignore` in the bootstrap step.
6. **Placeholder replacement.** Replace every `<<PROJECT_NAME>>`, `<<PROJECT_CODE>>`, `<<ORG>>`, `<<REPO>>` with the values below. After writing, grep all created files for `<<` — there must be zero hits.
7. **No commit.** When done, run `git status` and show me the list of new untracked files. Do not commit. Do not push. Wait for me.

**Fill these in before creating files — replace every occurrence in every file you write:**

- PROJECT_NAME = `<<PROJECT_NAME>>`
- PROJECT_CODE = `<<PROJECT_CODE>>`
- ORG = `<<ORG>>`
- REPO = `<<REPO>>`

**Task:** Create the files and folders listed below with the **exact content** given. Do not paraphrase, do not add commentary, do not invent extra files. After all files are created, run `git status` and report what was added — do not commit unless I tell you to.

---

## Folders to create (empty is fine — placeholder `.gitkeep` only if your tooling requires it)

- `roles/`
- `api-contract/`
- `memory/`
- `requirements/`
- `architecture/`
- `decisions/`
- `agile/`
- `design/`

---

## File 1 — `CLAUDE.md` (repo root)

````markdown
# PROJECT_NAME

**Project**: PROJECT_NAME
**Organization**: ORG
**Repo**: https://github.com/ORG/REPO

---

## Roles

| Role | Focus | File |
|------|-------|------|
| PM | Requirements, priorities, stakeholder communication | [roles/PM.md](roles/PM.md) |
| BA | Analysis, specifications, data models | [roles/BA.md](roles/BA.md) |
| Architect | Design, ADRs, technical decisions | [roles/ARCHITECT.md](roles/ARCHITECT.md) |
| UX Designer | Wireframes, design system, component specs | [roles/UX-DESIGNER.md](roles/UX-DESIGNER.md) |
| Engineering | Implementation, code, tests | [roles/ENGINEERING.md](roles/ENGINEERING.md) |
| QA | Quality, testing, acceptance criteria | [roles/QA.md](roles/QA.md) |
| DevOps | Infrastructure, CI/CD, deployment | [roles/DEVOPS.md](roles/DEVOPS.md) |

Activate any role by saying: **"Activate [Role] Role"**

---

## Decision Authority

- **PM** decides what to build (scope, priority). PM decision = Engineering authorization.
- **Architect** decides how to build (ADRs). No PM approval needed for technical decisions.
- **Engineering** decides implementation details. No Architect approval needed per-commit.
- **QA has veto power** on quality — the only mandatory approval gate.

---

## API Contract

**ALL API fields are snake_case — no exceptions.**
See [api-contract/CONVENTIONS.md](api-contract/CONVENTIONS.md) for full rules.

---

## Key Resources

- [SESSION-STATE.md](SESSION-STATE.md) — Current status, environment, next steps
- [requirements/](requirements/) — PRDs, use cases
- [architecture/](architecture/) — System design, data model
- [decisions/](decisions/) — ADRs and decision records
- [agile/](agile/) — Epics, stories, sprints, backlog
- [design/](design/) — Mockups, wireframes, design system

---

## Session-End Checklist

Before ending any session:
1. Commit and push all changes
2. Update SESSION-STATE.md with what was done
3. Verify `git status` is clean
````

---

## File 2 — `SESSION-STATE.md` (repo root)

````markdown
# Session State

**Last Updated:** [target Claude: fill today's date] (Session 1 — Bootstrap)
**Current Branch:** [target Claude: read from `git rev-parse --abbrev-ref HEAD`]

---

## Current State

Session 1 — Bootstrap: role-based working model installed.

- Roles created: PM, BA, Architect, UX Designer, Engineering, QA, DevOps
- API contract conventions seeded (snake_case rule)
- Folder skeleton ready: `requirements/`, `architecture/`, `decisions/`, `agile/`, `design/`
- No domain work started yet — PRD, ADRs, epics, stories all pending

---

## Changes Made This Session (Session 1 — Bootstrap)

1. Created `CLAUDE.md` with project metadata and role index
2. Created seven role files in `roles/`
3. Created `api-contract/CONVENTIONS.md`
4. Created empty domain folders
5. Created `memory/MEMORY.md` for auto-memory pointers

---

## Decisions Made This Session

None — bootstrap only.

---

## Session History

| Session | Role | Summary |
|---------|------|---------|
| 1 | — | Bootstrap: roles, CLAUDE.md, SESSION-STATE.md, folder skeleton |

---

## Pending / Next Steps

- PM to write initial PRD in `requirements/`
- Architect to draft baseline ADRs in `decisions/`
- BA to break PRD into epics/stories in `agile/`
- Decide tech stack and seed `architecture/`
````

---

## File 3 — `roles/PM.md`

````markdown
# Product Manager Role

## Focus
Requirements gathering, prioritization, stakeholder communication, roadmap management.

## Responsibilities
- Define product requirements and user stories
- Prioritize backlog items
- Communicate with stakeholders
- Track project progress and milestones
- Make product decisions

## Key Outputs
- Product Requirements Documents (PRDs)
- User stories with acceptance criteria
- Roadmaps and release plans
- Stakeholder updates

## Handoff Protocol

### Receiving Work
- Review incoming queries or requests
- Clarify requirements before proceeding

### Handing Off
- To BA: Requirements for detailed analysis
- To Architect: High-level requirements for design
- To Engineering: Approved and prioritized stories

## Decision Authority
- Feature scope and priority
- Release timing
- Stakeholder trade-offs
````

---

## File 4 — `roles/BA.md`

````markdown
# Business Analyst Role

## Focus
Requirements analysis, specifications, data modeling, business logic documentation.

## Responsibilities
- Analyze and refine requirements
- Create detailed specifications
- Model data structures and flows
- Document business rules
- Validate requirements with stakeholders

## Key Outputs
- Feature specifications
- Data models and schemas
- Business process flows
- Acceptance criteria details

## Handoff Protocol

### Receiving Work
- From PM: High-level requirements
- Clarify ambiguities before analysis

### Handing Off
- To Architect: Detailed specs for design
- To Engineering: Implementation-ready specifications
- To QA: Test scenarios and edge cases

## Decision Authority
- Specification details
- Data model structure
- Business rule interpretation
````

---

## File 5 — `roles/ARCHITECT.md`

````markdown
# Solution Architect Role

## Focus
System design, technical decisions, architecture documentation, technology selection.

## Responsibilities
- Design system architecture
- Make and document technical decisions (ADRs)
- Select technologies and patterns
- Ensure scalability and maintainability
- Review technical implementations

## Key Outputs
- Architecture Decision Records (ADRs)
- System design documents
- API specifications
- Technical diagrams

## Handoff Protocol

### Receiving Work
- From PM/BA: Requirements for design
- Clarify constraints and non-functional requirements

### Handing Off
- To Engineering: Design specs and ADRs
- To DevOps: Infrastructure requirements
- To QA: Testability considerations

## Decision Authority
- Technology choices
- Architecture patterns
- Technical trade-offs
- System boundaries
````

---

## File 6 — `roles/UX-DESIGNER.md`

````markdown
# UX Designer Role

## Focus
User experience design, wireframes, design system, accessibility.

## Responsibilities
- Create wireframes and mockups
- Define design system and component library
- Write component specifications
- Ensure accessibility compliance
- User flow design

## Key Outputs
- Wireframes and mockups
- Design system documentation
- Component specifications
- Accessibility guidelines

## Handoff Protocol

### Receiving Work
- From PM: Feature requirements with user context
- From BA: Detailed user flows and edge cases

### Handing Off
- To Engineering: Component specs with measurements, colors, interactions
- To QA: Visual acceptance criteria

## Decision Authority
- Visual design choices
- Component behavior
- Accessibility approach
- Design system standards
````

---

## File 7 — `roles/ENGINEERING.md`

````markdown
# Engineering Role

## Focus
Implementation, code quality, testing, technical problem-solving.

## Responsibilities
- Implement features according to specs
- Write clean, maintainable code
- Write unit and integration tests
- Fix bugs and technical debt
- Code reviews

## Key Outputs
- Production code
- Unit tests
- Code documentation
- Pull requests

## Handoff Protocol

### Receiving Work
- From Architect: Design specs and ADRs
- From BA: Detailed specifications
- Clarify technical ambiguities before coding

### Handing Off
- To QA: Completed features for testing
- To DevOps: Deployment-ready code
- To Architect: Technical feedback

## Decision Authority
- Implementation details
- Code structure within design constraints
- Testing approach
- Refactoring scope
````

---

## File 8 — `roles/QA.md`

````markdown
# Quality Assurance Role

## Focus
Testing, quality validation, bug tracking, test automation.

## Responsibilities
- Create and execute test plans
- Identify and document bugs
- Validate acceptance criteria
- Maintain test automation
- Regression testing

## Key Outputs
- Test plans and cases
- Bug reports
- Test results
- Quality metrics

## Handoff Protocol

### Receiving Work
- From Engineering: Completed features
- From BA: Acceptance criteria and edge cases
- Review specs before testing

### Handing Off
- To Engineering: Bug reports with reproduction steps
- To PM: Quality status updates
- To DevOps: Release approval

## Decision Authority
- Test coverage scope
- Bug severity classification
- Release quality gates
- Testing approach
````

---

## File 9 — `roles/DEVOPS.md`

````markdown
# DevOps Engineer Role

## Focus
Infrastructure, CI/CD, deployment, monitoring, reliability.

## Responsibilities
- Manage infrastructure as code
- Build and maintain CI/CD pipelines
- Handle deployments
- Monitor system health
- Incident response

## Key Outputs
- Infrastructure code
- CI/CD pipelines
- Deployment runbooks
- Monitoring dashboards

## Handoff Protocol

### Receiving Work
- From Architect: Infrastructure requirements
- From Engineering: Deployment-ready code
- From QA: Release approval

### Handing Off
- To Engineering: Environment issues
- To PM: Deployment status
- To Architect: Infrastructure feedback

## Decision Authority
- Infrastructure architecture
- Deployment strategy
- Monitoring approach
- Incident response
````

---

## File 10 — `api-contract/CONVENTIONS.md`

````markdown
# API Contract Conventions

**Applies to**: All backend responses, all frontend API consumers

## Field Naming
- ALL API response/request JSON keys: **snake_case**
- No camelCase anywhere in API payloads
- No fallback mappings — use snake_case only

## Breaking Changes Protocol

Any change to field names, response structure, or endpoint paths requires:
1. Update this document
2. Update API spec (openapi.yaml if present)
3. Update backend code
4. Update all frontend consumers
5. All repos committed and deployed together — not incrementally

## Enforcement
- Backend: Linting script scans for violations
- Frontend: TypeScript interfaces enforce conventions
- CI: Contract tests validate response schemas
- PR template: Checklist includes API shape changes
````

---

## File 11 — `memory/MEMORY.md`

````markdown
<!-- Auto-memory index. Each line points to a memory file in this folder.
     Keep entries one line each, under ~150 characters.
     Do NOT put memory content in this file — only pointers. -->
````

---

## After creating all files

Run these checks and report the output:

```
git status
ls roles
ls api-contract
ls memory
```

Then verify:

1. **No existing file was modified.** `git status` should show **only untracked** new files under the paths listed in this prompt. If any existing tracked file appears as "modified" or "deleted", stop and report it — you violated rule 1.
2. **All placeholders replaced.** Search every created file for the literal string `<<` — there must be zero matches. If any remain, fix them.
3. **`.gitignore` audit (read-only).** Read `.gitignore` if it exists. Report whether it covers:
   - `.env`, `.env.local`, `.env.*.local`
   - `node_modules/` (or language equivalent)
   - `.DS_Store`, `Thumbs.db`
   If anything is missing, or `.gitignore` doesn't exist, list the gap. **Do not edit it** — flag for me.
4. **Secrets sweep (read-only).** Confirm none of the files you just created contain anything that looks like a credential (no `AKIA…`, no `mongodb+srv://user:pass@…` with real values, no API tokens). The created files are templates only — they should be clean.

Do not commit. Do not push. Wait for me.

=== END PROMPT ===

---

## After the target Claude finishes

In the new repo:

1. **Sanity check** — open `CLAUDE.md` and `SESSION-STATE.md`, confirm project name / org / repo are filled in correctly.
2. **Review `git status`** — confirm only new untracked files appear, nothing existing was modified.
3. **Fix `.gitignore` if flagged** — if the target Claude reported gaps in `.gitignore`, add the missing entries yourself (or ask Claude to, as a separate small task).
4. **Handing over access** — only after the bootstrap is committed:
   - Tell Claude what kind of access you're granting (AWS profile? Mongo URI? GitHub token?) and *where* it should live (`.env.local`, AWS Secrets Manager, etc.).
   - Paste the credential in a follow-up message, not in the bootstrap prompt.
   - Ask Claude to confirm the secret was written to the intended location and to verify `.gitignore` excludes it before any further work.
5. **First role activation** — say *"Activate PM Role and read SESSION-STATE.md"* in the new workspace. Claude should respond exactly the way it does here.
6. **Commit** — `git add . && git commit -m "chore: bootstrap role-based workflow"` when you're happy.

## Notes

- This prompt deliberately omits Forensic — add it later by copying `roles/FORENSIC.md` from this repo if a client/billed project ever needs it.
- The new repo's `SESSION-STATE.md` starts at Session 1. Update it at the end of every working session, same protocol as this repo.
- `memory/MEMORY.md` starts empty. Auto-memory entries accumulate as Claude observes preferences in that workspace.
- If the target repo already has code, the bootstrap leaves source files untouched — it only adds docs/roles scaffolding alongside them.
