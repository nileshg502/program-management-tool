# Program Management Tool

A structured project management workspace using Claude-assisted agile practices.

## Overview

This repository is the single source of truth for all planning, requirements, architecture, and delivery artifacts for the Program Management Tool project.

## Structure

```
program-management-tool/
├── CLAUDE.md                          # AI assistant context
├── README.md                          # This file
├── REPOSITORIES.md                    # Multi-repo inventory
├── SESSION-STATE.md                   # Live session tracking
├── roles/                             # Role definitions
├── decisions/                         # ADRs and decision records
├── requirements/                      # PRDs and requirements
├── architecture/                      # System design
├── api-contract/                      # API naming conventions
├── agile/                             # Sprints, stories, backlogs
├── change-requests/                   # Formal change requests
├── design/                            # UI/UX specs
├── test-plans/                        # QA test plans
├── flows/                             # Business process flows
├── docs/                              # Mockups, previews
└── compliance/                        # Audit logs
```

## Quick Start

In Claude, activate a role to begin:
- `Activate PM role` — start with requirements and roadmap
- `Activate BA role` — analyze and spec features
- `Activate Architect role` — design system and record ADRs
- `Activate Engineering role` — implement features
- `Activate QA role` — test and validate quality
- `Activate DevOps role` — infrastructure and deployment

## Key Principles

1. Single source of truth per artifact
2. Explicit decision authority — no ambiguity about who decides what
3. SESSION-STATE.md updated every session for seamless continuity
4. QA has veto power — the only mandatory quality gate
5. Stories >13 points must be split
