# ADR-001 — Tech Stack Selection

**Date:** 2026-05-05
**Status:** Approved
**Decider:** Architect + PM

---

## Context

Trackwise is a multi-tenant program management web application serving 5–8 organisations at launch. The engineering team works in Node.js and React. The deployment target is AWS. Cost-effectiveness is a primary constraint at this scale.

---

## Decision

| Layer | Choice |
|-------|--------|
| Backend framework | NestJS + TypeScript |
| Backend runtime | Node.js on AWS Lambda |
| API routing | AWS API Gateway (HTTP API) |
| Background jobs | AWS EventBridge Scheduler + Lambda |
| Frontend | React + TypeScript |
| Frontend hosting | AWS S3 + CloudFront |
| Database | MongoDB Atlas (M0 free tier) |
| ODM | Mongoose via @nestjs/mongoose |
| CI/CD | GitHub Actions |

---

## Rationale

### Node.js + NestJS on Lambda
- Node.js is the team's primary runtime — no learning curve on the language.
- NestJS is built for TypeScript and provides module structure and dependency injection — appropriate for a multi-tenant app with 8 epics of complexity.
- Lambda eliminates always-on infrastructure cost. At 5–8 orgs with low concurrent traffic, Lambda is significantly cheaper than ECS Fargate (~$0 vs ~$30–50/month idle cost).
- API Gateway HTTP API is used (not REST API) — 70% cheaper for the same routing capability at this scale.

### EventBridge Scheduler + Lambda for GitHub sync
- GitHub data must refresh every N minutes (configurable per org).
- EventBridge Scheduler triggers a dedicated sync Lambda on a per-org schedule.
- Fully managed, no worker process running 24/7, cost is negligible at this scale.

### React + TypeScript on S3 + CloudFront
- React is the team's frontend framework.
- TypeScript adds type safety across a data-heavy dashboard UI.
- S3 + CloudFront is the standard, lowest-cost static hosting pattern on AWS.

### MongoDB Atlas (M0 free tier)
- Team preference for MongoDB over PostgreSQL.
- Atlas M0 free tier is sufficient for 5–8 orgs at launch (512MB storage).
- Upgrade path to M10+ is available when scale requires it.
- Mongoose is the standard ODM for MongoDB on NestJS — uses `@nestjs/mongoose` module.

### GitHub Actions for CI/CD
- Repository is already on GitHub.
- No additional tooling or cost required.

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| ECS Fargate | ~$30–50/month idle cost — over-engineered at 5–8 orgs |
| AWS Lambda (REST API Gateway) | HTTP API is 70% cheaper with equivalent capability |
| Express (instead of NestJS) | NestJS provides better structure for TypeScript and multi-tenant complexity |
| PostgreSQL on RDS | Team prefers MongoDB; relational overhead not needed for chosen stack |
| Prisma ORM | Prisma MongoDB connector lacks migration support; Mongoose is more mature for MongoDB |
| AWS DocumentDB | $50+/month minimum — breaks cost constraint; Atlas free tier available |
| BullMQ / SQS worker | Adds Redis or always-on worker cost; EventBridge + Lambda is managed and cheaper |

---

## Known Limitations and Scaling Decisions

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| MongoDB Atlas M0 — no VPC peering | Lambda connects to Atlas over public internet with TLS — acceptable for v1.0 | Upgrading to M10+ |
| MongoDB Atlas M0 — 512MB storage limit | Sufficient for 5–8 orgs at launch | Approaching 400MB used |
| Lambda cold starts (200–800ms on Node.js) | Acceptable for dashboard use case at this scale | User complaints or SLA requirement |
| No RDS Proxy equivalent for MongoDB | Mongoose connection reuse per Lambda instance — manageable at low concurrency | 20+ orgs with sustained traffic |

---

## Consequences

- Engineering must structure NestJS app to bundle and deploy on Lambda (use `@nestjs/platform-express` with `serverless-http` adapter).
- GitHub sync polling interval must be stored per org in MongoDB — not hardcoded.
- CloudFront distribution requires cache invalidation on each frontend deploy — must be part of CI/CD pipeline.
- All Lambda failures (API and sync) must be observable — CloudWatch Logs and Lambda Dead Letter Queue required from day one.
- Atlas M0 connection limit is 500 — monitor connections as Lambda concurrency grows.

---

## Review Trigger

Revisit this ADR when:
- Organisation count exceeds 20
- Atlas M0 storage approaches 400MB
- Cold start latency becomes a user complaint
