# ST-010 — Search and filter directory by role, domain, availability

**Story ID:** ST-010
**Epic:** EP-02 — People Directory
**Priority:** P1
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to search and filter the people directory by role, domain, and availability, so that I can quickly find the right person when staffing a new project.

---

## Acceptance Criteria

- [ ] Free-text search filters employees by name in real time (client-side)
- [ ] Filter by role — dropdown with all role values; selecting one shows only employees with that role
- [ ] Filter by domain — dropdown or tag selector; selecting a domain shows only employees who have that domain knowledge entry
- [ ] Filter by availability — options: All / Available / Assigned / Partially Available
- [ ] Multiple filters can be applied simultaneously — results are the intersection
- [ ] Filter state is reflected in the URL (query params) so links are shareable
- [ ] Clearing all filters returns the full directory
- [ ] Empty state shown when no employees match the active filters

---

## Notes

- Domain filter dropdown should be populated from all distinct `domain_name` values currently in the database — not a hardcoded list
