# System Overview — Trackwise

**Last Updated:** 2026-05-06
**Status:** Approved
**Version:** v1.0

---

## Purpose

Trackwise is a multi-tenant program management tool for software delivery organisations. It gives management (CTO, CEO, PM) a single place to see client and project health, team composition, domain expertise, scope deviation, and GitHub delivery status — without manually checking GitHub or chasing status updates.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      Users (Browser)                     │
│              React + TypeScript SPA                      │
│         Ant Design  |  Redux Toolkit  |  React Router    │
└────────────────────────┬────────────────────────────────┘
                         │ HTTPS
                         ▼
┌─────────────────────────────────────────────────────────┐
│                  AWS CloudFront + S3                     │
│              Static frontend hosting + CDN               │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│             AWS API Gateway (HTTP API)                   │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              AWS Lambda — API Function                   │
│           NestJS + TypeScript + Mongoose                 │
│       Auth: AWS Cognito JWT verification                 │
└────────────────────────┬────────────────────────────────┘
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
┌─────────────────────┐   ┌─────────────────────────────┐
│   MongoDB Atlas     │   │     AWS Cognito              │
│   (M0 Free Tier)    │   │     User Pool                │
│   Single database   │   │     JWT issuing + invite     │
│   org_id on every   │   └─────────────────────────────┘
│   document          │
└─────────────────────┘

┌─────────────────────────────────────────────────────────┐
│         AWS EventBridge Scheduler                        │
│         Triggers sync per org (configurable interval)    │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              AWS Lambda — Sync Function                  │
│   1. Reads GitHub milestones, sprint labels, blocked     │
│   2. Reads cr-tracker.md from repo (forensic data)       │
│   3. Evaluates alert conditions                          │
│   4. Writes results to MongoDB Atlas                     │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              GitHub (OAuth App)                          │
│   Milestones API  |  Issues API  |  Contents API         │
└─────────────────────────────────────────────────────────┘
```

---

## Key Components

| Component | Technology | Responsibility |
|-----------|-----------|----------------|
| Frontend SPA | React + TypeScript, Ant Design, Redux Toolkit, React Router v6 | User interface — dashboard, project detail, people directory, alerts |
| Frontend hosting | AWS S3 + CloudFront | Static file serving, CDN, HTTPS |
| API Gateway | AWS API Gateway (HTTP API) | Route HTTP requests to API Lambda |
| API Lambda | NestJS + TypeScript on AWS Lambda | All REST endpoints, business logic, auth enforcement |
| Sync Lambda | Node.js on AWS Lambda | GitHub data sync, forensic parsing, alert evaluation |
| Scheduler | AWS EventBridge Scheduler | Triggers sync Lambda per org on configurable interval |
| Auth | AWS Cognito User Pool | User authentication, JWT issuing, invite email |
| Database | MongoDB Atlas M0 | All application data — single database, org_id tenant isolation |
| ODM | Mongoose via @nestjs/mongoose | Schema definition, query layer |
| GitHub integration | GitHub OAuth App | Read milestones, sprint labels, blocked issues, cr-tracker.md |
| CI/CD | GitHub Actions | Build, test, deploy frontend and Lambda functions |
| Secrets | AWS Secrets Manager | GitHub OAuth credentials, Cognito secrets |
| Observability | AWS CloudWatch + Lambda DLQ | Lambda logs, sync failure capture |

---

## Request Flow — Dashboard Load

```
1. User opens browser → CloudFront serves React SPA from S3
2. React app loads → Redux fetches Cognito JWT from Amplify session
3. RTK Query calls API Gateway with Authorization: Bearer <token>
4. API Gateway invokes API Lambda
5. NestJS JwtAuthGuard verifies token against Cognito JWKS
6. org_id extracted from JWT → injected into request context
7. NestJS service queries MongoDB — all queries scoped to org_id
8. Response returned → Redux store updated → UI renders
```

## Request Flow — GitHub Sync

```
1. EventBridge Scheduler fires on configured interval (or PM clicks Sync Now)
2. Sync Lambda invoked with org context
3. Lambda reads GitHub milestones, sprint labels, blocked issues via GitHub API
4. Lambda reads cr-tracker.md via GitHub Contents API
5. Lambda parses cr-tracker.md Totals table → extracts CR summary
6. Lambda compares new milestone/sprint status against previous → detects transitions
7. Alert conditions evaluated → new alerts written to alerts collection if conditions met
8. MongoDB updated: milestones, sprints, cr_summaries, last_synced_at, sync_status
9. Frontend reflects updated data on next page load or navigation
```

---

## Non-Functional Requirements

| Requirement | Target | Notes |
|-------------|--------|-------|
| Availability | AWS Lambda SLA (~99.95%) | No always-on infrastructure to manage |
| Latency | < 800ms API response (p95) | Lambda cold starts acceptable at v1.0 scale |
| Scale (v1.0) | 5–8 organisations | MongoDB Atlas M0, Lambda concurrency sufficient |
| GitHub sync interval | 5–60 minutes, configurable per org | Default 15 minutes |
| Auth token expiry | 1 hour access token, 30 day refresh | Cognito defaults |
| Cost (v1.0 estimate) | ~$33–45/month | Lambda + S3 + CloudFront + Atlas M0 + Cognito free tier |
| Data isolation | org_id on every document, enforced at service layer | Single MongoDB database, multi-tenant |

---

## Scaling Limitations (v1.0 Known)

| Limitation | Trigger to Revisit |
|------------|-------------------|
| MongoDB Atlas M0 — 512MB storage | Approaching 400MB used |
| No VPC peering — Lambda to Atlas over public internet with TLS | Upgrading to Atlas M10+ |
| Lambda cold starts 200–800ms | User complaints or SLA requirement |
| No RDS Proxy equivalent — Mongoose connection_limit=1 per instance | Lambda concurrency consistently above 100 |
| Single Manager role — no permission granularity | Viewer or restricted role requirement |
| In-app alerts only — no email | User demand for email notifications |

---

## ADR Index

| ADR | Title | Status |
|-----|-------|--------|
| [ADR-001](../decisions/architect/ADR-001-tech-stack-selection.md) | Tech Stack Selection | Approved |
| [ADR-002](../decisions/architect/ADR-002-database-and-data-layer.md) | Database and Data Layer | Approved |
| [ADR-003](../decisions/architect/ADR-003-authentication-and-rbac.md) | Authentication and RBAC | Approved |
| [ADR-004](../decisions/architect/ADR-004-github-integration-strategy.md) | GitHub Integration Strategy | Approved |
| [ADR-005](../decisions/architect/ADR-005-frontend-architecture.md) | Frontend Architecture and Routing | Approved |
| [ADR-006](../decisions/architect/ADR-006-notification-and-alert-delivery.md) | Notification and Alert Delivery | Approved |
| [ADR-007](../decisions/architect/ADR-007-forensic-data-source.md) | Forensic Data Source | Approved |
