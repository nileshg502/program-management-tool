# ADR-003 — Authentication and RBAC

**Date:** 2026-05-06
**Status:** Approved
**Decider:** Architect + PM

---

## Context

Trackwise is a multi-tenant application. Users need to authenticate and access data scoped to their organisation. The PRD defines CTO, CEO, and PM as distinct organisational roles, but access permissions are identical across all three. Employees do not log in — their details are managed by PM.

---

## Decision

### Authentication Provider
AWS Cognito — one User Pool per Trackwise deployment (not per tenant).

### User Roles
Single application role: **Manager**

CTO, CEO, and PM are organisational titles only — all have identical system permissions. No technical role distinction is required in v1.0.

### Permissions — Manager Role

| Feature | Access |
|---------|--------|
| Portfolio dashboard — all clients and projects | Yes |
| Project detail view | Yes |
| Assign / remove team members from projects | Yes |
| People directory | Yes |
| Create / edit employee records and domain knowledge | Yes |
| Forensic summary | Yes |
| Alerts — all projects | Yes |
| Create new users | Yes |

### Employee Access
Employees have no login. PM creates and maintains all employee records including domain knowledge entries directly in Trackwise.

### User Onboarding — Direct Creation
- Any Manager (CTO, CEO, or PM) creates a new user in-app by entering name, email, and password
- Trackwise calls Cognito `adminCreateUser` API with the provided password — no invite email sent
- User is immediately active — no first-login flow or temporary password
- User can change their own password after logging in via in-app settings
- Account deletion and password reset by admin handled via Cognito console for v1.0

### Multi-Tenancy — User to Org Binding
- Each Cognito user has a custom attribute: `custom:org_id`
- `org_id` is set at creation time from the creating Manager's org context
- Every API request extracts `org_id` from the verified JWT — injected into NestJS request context via a global guard
- No query executes without `org_id` from the JWT — enforced at the NestJS service layer (see ADR-002)

### Token Strategy
- Cognito issues JWT (ID token + Access token) on login
- Access token is sent as `Authorization: Bearer <token>` on every API request
- Lambda verifies the token against Cognito's JWKS endpoint
- Token expiry: 1 hour (Cognito default) — refresh token valid for 30 days
- NestJS `@nestjs/passport` + `passport-jwt` handles token verification

---

## Rationale

### Single Manager role
The PRD RBAC table distinguished CTO/CEO from PM but the PM confirmed permissions are identical. A single role reduces complexity — no role-based conditional rendering or API guards needed beyond authentication.

### AWS Cognito
- Managed auth service — no credential storage, no password hashing, no token signing built in-house
- Native AWS integration — Lambda verifies Cognito JWTs without additional infrastructure
- Free tier covers 50,000 MAUs — sufficient for 5–8 orgs
- `adminCreateUser` supports direct user creation with a permanent password — no invite email required

### org_id in JWT custom attribute
- Binds the user to their tenant at auth time — no lookup needed on every request
- Cannot be spoofed — JWT is signed by Cognito
- NestJS global guard extracts and injects it before any controller executes

### No employee login
- Reduces auth surface area for v1.0
- PM manages all employee data — simpler data ownership model
- Employee self-service (domain knowledge editing) deferred to v2

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| Email + password (custom built) | Builds auth from scratch — credential storage, hashing, token signing, password reset all custom. Cognito handles this for free |
| Google SSO | Valid option but adds OAuth complexity for v1.0; Cognito supports adding Google SSO later without architecture change |
| Per-tenant Cognito User Pool | Cleaner isolation but management overhead grows with org count; single pool with org_id attribute is sufficient at this scale |
| Invite flow with temporary password | Adds unnecessary friction — Manager sets password directly, user is immediately active |
| Multiple roles (CTO / PM / Viewer) | PM confirmed all three roles have identical permissions — unnecessary complexity |

---

## Known Limitations and Scaling Decisions

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| No employee self-service login | PM manages all employee records | v2 — add Employee role with scoped access to own profile and domain knowledge |
| Account deletion via Cognito console only | Acceptable at 5–8 orgs — no offboarding UI needed | Org count grows and offboarding becomes frequent |
| Single Manager role — no permission granularity | Sufficient for v1.0 scope | Requirement emerges for read-only viewer or restricted PM access |
| Google SSO not in v1.0 | Cognito User Pool supports adding identity providers later without architecture change | User demand for SSO |

---

## Consequences

- EP-01 requires a new story: user creation flow — any Manager (CTO, CEO, or PM) creates a new user by entering name, email, and password; backend calls Cognito `adminCreateUser` with permanent password; user is immediately active.
- NestJS must implement a global `JwtAuthGuard` that verifies Cognito JWT and extracts `org_id` into request context on every request.
- Cognito User Pool must be provisioned with `custom:org_id` attribute before any user is created.
- Frontend must implement Cognito Hosted UI or a custom login page using AWS Amplify SDK.
- Refresh token logic must be handled on the frontend — Amplify SDK manages this automatically.

---

## Review Trigger

Revisit this ADR when:
- Employee self-service login is required
- A read-only viewer role is requested
- Google or Microsoft SSO is requested
- Organisation count exceeds 20 and per-tenant isolation becomes a compliance requirement
