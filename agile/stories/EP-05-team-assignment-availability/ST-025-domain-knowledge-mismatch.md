# ST-025 — Domain knowledge mismatch flag on project

**Story ID:** ST-025
**Epic:** EP-05 — Team Assignment & Availability
**Priority:** P2
**Points:** 5
**Status:** Not Started

---

## User Story

As a CTO/CEO or PM, I need the system to flag when a project requires domain knowledge that no current team member has, so that I can identify skill gaps before they become delivery risks.

---

## Acceptance Criteria

- [ ] Project entity supports a `required_domains` field — a list of domain names the project needs
- [ ] Admin or PM can set `required_domains` on a project
- [ ] System checks: for each required domain, is there at least one active team member who has that domain in their domain knowledge?
- [ ] If a gap exists — a warning banner appears on the project team section: "Domain gap: [domain name] — no team member has this domain"
- [ ] If all required domains are covered — no warning shown
- [ ] Gap check re-runs automatically when team membership changes or domain knowledge entries change
- [ ] Alert generated for PM when a gap exists (consumed by EP-07 ST-039)

---

## Notes

- `required_domains` is optional — if not set, no mismatch check is performed
- Domain matching is exact string match on `domain_name` — case insensitive
