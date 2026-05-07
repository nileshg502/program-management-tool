# ADR-005 — Frontend Architecture and Routing

**Date:** 2026-05-06
**Status:** Approved
**Decider:** Architect + PM

---

## Context

Trackwise frontend is a React + TypeScript single page application hosted on AWS S3 + CloudFront (decided in ADR-001). This ADR defines the frontend architecture — state management, UI component library, routing, and data fetching strategy.

---

## Decision

### State Management
Redux Toolkit — single store for all application state.

| State Type | Handled By | Examples |
|------------|-----------|---------|
| Server data | Redux Toolkit (RTK Query) | Projects, clients, employees, milestones, sprints, alerts |
| Auth / user context | Redux Toolkit slice | Current user, org_id, role |
| UI state | Redux Toolkit slice | Sidebar open/closed, active filters, loading flags |

### UI Component Library
Ant Design — used for all UI components.

Rationale: Ant Design is purpose-built for data-heavy management dashboards. It provides tables, cards, status badges, notification components, form elements, and layout primitives out of the box — reducing custom component work significantly.

### Routing
React Router v6 — client-side routing for all pages.

### Data Fetching Strategy
- Data is fetched on page/component mount via RTK Query endpoints
- No automatic background polling on the frontend
- GitHub data is synced on the backend every N minutes (configurable per org — ADR-004)
- Frontend reflects the latest synced data when the user loads or navigates to a page
- After a manual "Sync Now" action — frontend re-fetches project data once sync Lambda confirms completion

---

## Route Structure

| Route | Component | Access |
|-------|-----------|--------|
| `/login` | Login page | Public |
| `/` | Portfolio dashboard — all clients and projects | Manager |
| `/projects/:projectId` | Project detail view | Manager |
| `/people` | People directory | Manager |
| `/people/:employeeId` | Employee detail / edit | Manager |
| `/settings` | Org settings — sync interval, GitHub connection | Manager |
| `/settings/users` | User management — invite flow | Manager |

---

## Redux Store Structure

```
store/
├── auth/         — current user, org_id, token
├── clients/      — client list and client detail
├── projects/     — project list, project detail, sync status
├── employees/    — people directory, employee detail
├── milestones/   — milestones per project
├── sprints/      — current sprint per project
├── alerts/       — alerts per project
└── ui/           — sidebar, filters, loading flags
```

---

## Folder Structure

```
src/
├── app/              — Redux store setup, RTK Query base API
├── features/         — one folder per domain (auth, projects, people, alerts)
│   └── projects/
│       ├── projectsSlice.ts
│       ├── projectsApi.ts
│       └── components/
├── pages/            — one file per route
├── components/       — shared UI components
├── layouts/          — page layout wrappers
├── hooks/            — shared custom hooks
├── utils/            — helpers, formatters
└── constants/        — enums, config values
```

---

## Rationale

### Redux Toolkit over React Query
- Team preference and familiarity
- No background polling needed on the frontend — React Query's primary advantage (automatic background refetch) is not required
- RTK Query (built into Redux Toolkit) handles API calls, caching, loading and error states cleanly
- Single store gives a predictable, debuggable state tree across the entire app

### Ant Design over shadcn/ui or MUI
- Trackwise is a data-heavy management dashboard — Ant Design's table, card, badge, and notification components match the UI requirements directly
- Reduces custom component work for data grids, status indicators, and form layouts
- Well-documented, stable, TypeScript-first

### React Router v6
- Industry standard for React SPA routing
- Nested routes support the layout structure (authenticated shell wrapping all Manager routes)
- No alternatives considered — no constraint or preference against it

### On-demand data refresh only
- GitHub sync runs on the backend on a configurable schedule — frontend does not need to mirror this interval
- User loads a page → sees latest synced data
- User clicks "Sync Now" → sync Lambda runs → frontend re-fetches that project's data
- Eliminates complexity of frontend polling, WebSocket connections, or server-sent events for v1.0

---

## Alternatives Considered

| Alternative | Reason Not Chosen |
|-------------|------------------|
| React Query (TanStack Query) | Better fit for background polling — not needed; Redux Toolkit preferred by team |
| Zustand | Lightweight but lacks RTK Query's built-in API layer; Redux Toolkit covers both state and API |
| shadcn/ui | Requires more custom component work for data-heavy dashboard use cases |
| Material UI (MUI) | Google Material style — less suited to enterprise management dashboard aesthetic than Ant Design |
| Redux (without Toolkit) | Redux Toolkit is the modern standard — plain Redux is verbose and not recommended for new projects |

---

## Known Limitations

| Limitation | Mitigation | Revisit When |
|------------|-----------|--------------|
| No automatic UI refresh — user sees stale data until they navigate or sync | "Last synced at" timestamp on every project card (from ADR-004) tells user how fresh the data is | Real-time dashboard requirement emerges |
| Ant Design bundle size (~1.5MB minified) | Tree-shaking via `babel-plugin-import` — only imported components are bundled | Bundle size becomes a performance concern |

---

## Consequences

- Frontend is a pure SPA — all routing is client-side; S3 must serve `index.html` for all routes (CloudFront error page rule: 404 → index.html with 200 status)
- RTK Query base API must attach `Authorization: Bearer <token>` header and `org_id` context on every request
- Auth slice must hydrate from Cognito session on app load — use AWS Amplify SDK for Cognito token management
- Redux DevTools enabled in development, disabled in production build
- All Ant Design imports must use tree-shaking to keep bundle size manageable

---

## Review Trigger

Revisit this ADR when:
- Real-time dashboard updates are required — evaluate WebSockets or server-sent events
- Bundle size exceeds performance budget
- A new major React state management standard emerges and team prefers to migrate
