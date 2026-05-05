# Removal Task — [REM-ID]

Project        : [Project Name]
Project Code   : [TRW]
REM ID         : [REM-TRW-S2-001]
Date Created   : [YYYY-MM-DD]
Created By     : Forensic Role
CR Reference   : [CR-TRW-S2-001]
Sprint         : [Sprint number]

---

## What to Remove

Description    : [Exact description of what needs to be removed]
Location       : [File / module / function / component — be precise]
Added In       : [Sprint / Run where this was introduced]

---

## Dependencies

| Dependency | Type | Impact if Removed | Action Required |
|------------|------|------------------|----------------|
| [Component / function] | [calls it / is called by it] | [what breaks] | [what to do] |

---

## What to Preserve

[List everything that must NOT be touched during this removal — functions, flows, data, configs that share the same area of code]

---

## Removal Instructions

Step 1 : [Exact action — e.g., "Remove function biometricAuth() from auth.service.ts"]
Step 2 : [Next action]
Step 3 : [Next action]

---

## Verification Checklist

- [ ] Removed code no longer exists in codebase
- [ ] All dependencies listed above have been handled
- [ ] Existing functionality confirmed working — [list what to test]
- [ ] No regression in [related module / flow]
- [ ] PR reviewed before merge

---

## Sign-Off

Completed By   : [Engineer Name]
Completed On   : [YYYY-MM-DD]
Verified By    : [QA Name]
Verified On    : [YYYY-MM-DD]
