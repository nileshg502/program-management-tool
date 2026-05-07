# ST-028 — Filter availability view by domain and status

**Story ID:** ST-028
**Epic:** EP-05 — Team Assignment & Availability
**Priority:** P2
**Points:** 3
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need to filter the availability view by domain and availability status, so that when scoping a new project I can immediately find people with the right skills who are free.

---

## Acceptance Criteria

- [ ] Filter by availability status — options: All / Available / Partially Available / Assigned
- [ ] Filter by domain — dropdown populated from all distinct domain names in the system
- [ ] Filters can be combined — results are the intersection
- [ ] Filter state reflected in URL query params — shareable links
- [ ] Clearing filters returns full availability list
- [ ] Empty state shown when no employees match the active filters

---

## Notes

- Reuses domain dropdown data source from ST-010 — consider a shared API endpoint
