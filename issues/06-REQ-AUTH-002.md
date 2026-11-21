<!-- title: [REQ-AUTH-002] Login and logout manage sessions securely -->
<!-- labels: requirement -->

### Requirement ID

REQ-AUTH-002

### Title

Login and logout manage sessions securely

### Version

0.1.0

### Status

Draft

### Author

UNKNOWN

### Last Updated

2026-11-21

### Priority

P0 - Critical

### Classification

Security

### Description (RFC 2119)

The system MUST rotate the session identifier on successful login.
The system MUST invalidate the server-side session on logout.
Session cookies MUST be set with Secure, HttpOnly, and SameSite=Lax or stricter.
Login failure responses MUST NOT reveal whether the account exists.

### Rationale

Implements SECURITY.md > Authentication and sessions. Follows assumption A2 (Django sessions) until {{AUTH_MECHANISM}} is confirmed.

### Applies To

In scope: login and logout endpoints and session cookie settings.
Out of scope: MFA (pending {{AUTH_MECHANISM}}), rate limiting (REQ-AUTH-003).

### Components

Django REST API, React client

### Actors

Dog lover (authenticated), Dog seller, Shelter, Staff / admin

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Authentication / session

### Threats Addressed

Session fixation; session theft via XSS-readable cookies; account enumeration; sessions surviving logout.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

Each login creates a fresh session; a pre-login session ID grants nothing after authentication.

### OWASP ASVS

V3.2.1, V3.3.1, V3.4.1, V3.4.2, V3.4.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-12, SC-23, IA-2

### NIST SP 800-207

Tenet 3 - access is granted per session

### Regulatory

UNKNOWN

### Other

OWASP WSTG-IDNT-04 (account enumeration); OWASP API Security Top 10 - API2:2023 Broken Authentication

### AC-01 - Positive case

GIVEN a valid username and password
WHEN the user logs in
THEN a new session ID is issued that differs from any pre-login session, with Secure, HttpOnly, and SameSite=Lax or stricter

### AC-02 - Invalid / malicious input is rejected

GIVEN a wrong password for an existing account and any password for a non-existent account
WHEN each login is attempted
THEN both return the same status and body, and neither creates a session

### AC-03 - Negative case

GIVEN a user who has logged out
WHEN a request is replayed with the old session cookie
THEN access is denied (401/403) and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Cookie settings assertions.

### Integration

Login/logout flow tests covering AC-01..AC-03.

### Security

Session fixation test; enumeration response comparison.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{AUTH_MECHANISM}} must be confirmed (assumption A2)
REQ-HTTP-001 (login CSRF)
REQ-AUTH-003 (rate limiting)

### Downstream

- REQ-EPIC-001
- REQ-AUTH-004
- REQ-STORE-001
- REQ-LISTING-001
- REQ-CLIENT-002

### External

N/A

### Constraints

Use Django's `login()`/`logout()` so rotation and invalidation are handled by the framework.

### Anti-Patterns

- Storing session or auth tokens in localStorage
- Distinct messages for "unknown user" and "wrong password"

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
