# API Contract Conventions

**Effective**: 2026-05-01
**Applies to**: All backend responses, all frontend API consumers

## Field Naming
- ALL API response/request JSON keys: **snake_case**
- No camelCase anywhere in API payloads
- No fallback mappings — use snake_case only

## Breaking Changes Protocol

Any change to field names, response structure, or endpoint paths requires:
1. Update this document
2. Update API spec (openapi.yaml)
3. Update backend code
4. Update all frontend consumers
5. All repos committed and deployed together — not incrementally

## Enforcement
- Backend: Linting script scans for violations
- Frontend: TypeScript interfaces enforce conventions
- CI: Contract tests validate response schemas
- PR template: Checklist includes API shape changes
