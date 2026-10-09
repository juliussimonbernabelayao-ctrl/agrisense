# MAGRO Requirements

This document is the source of truth for functional requirements.

## Requirement format

Each requirement must contain:
- ID
- Description
- Priority
- Acceptance criteria
- Verification method

Example:

### AUTH-001 — Authentication

**Priority:** Critical

**Acceptance criteria**
- [ ] Valid users can authenticate.
- [ ] Invalid credentials are rejected.
- [ ] Passwords are securely stored.
- [ ] Protected resources reject unauthenticated requests.
- [ ] Logout invalidates the authenticated session.
- [ ] Automated tests cover success and failure cases.

**Verification**
Automated tests + independent checker.

---

## Requirements

Add the approved MAGRO requirements here before implementation begins.

Do not change acceptance criteria merely to make an implementation pass.
