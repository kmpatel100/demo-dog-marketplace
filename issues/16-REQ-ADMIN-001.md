<!-- title: [REQ-ADMIN-001] Django admin is restricted to staff and hardened -->
<!-- labels: requirement -->

### Requirement ID

REQ-ADMIN-001

### Title

Django admin is restricted to staff and hardened

### Version

0.1.0

### Status

Draft

### Author

UNKNOWN

### Last Updated

2026-11-21

### Priority

P1 - High

### Classification

Security

### Description (RFC 2119)

Django admin MUST be accessible only to staff accounts.
Django admin MUST NOT be served at the default `/admin/` path.
Django admin login MUST be rate-limited.
Every admin action MUST be logged.

### Rationale

Implements SECURITY.md > Authorization (admin) and Logging and errors (admin actions).

### Applies To

In scope: Django admin routing, access, login throttling, and audit.
Out of scope: MFA for staff (pending {{AUTH_MECHANISM}}); staff permissions ({{ROLE_MATRIX}}).

### Components

Django admin, Django REST API

### Actors

Staff / admin, Anonymous visitor

### Data Classification

Confidential (PII such as seller/shelter contact details)

### Defense Layer

Authentication / session, Authorization (object-level / role), Logging / monitoring / error handling

### Threats Addressed

Credential stuffing against admin; privilege misuse by non-staff accounts; unaudited data changes.

### Trust Boundary

Staff -> Django admin

### Zero Trust Consideration

Staff status is checked on every admin request, and every admin change is attributable to a named account.

### OWASP ASVS

V4.1.1, V2.2.1, V7.2.2 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-3, AC-6(9), AC-7, AU-2

### NIST SP 800-207

Tenet 6 - authentication and authorization are dynamic and strictly enforced

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API5:2023 Broken Function Level Authorization

### AC-01 - Positive case

GIVEN a staff account
WHEN it logs in to admin at the configured path
THEN access is granted

### AC-02 - Invalid / malicious input is rejected

GIVEN repeated failed admin logins over the configured limit
WHEN another attempt is made
THEN it is rejected (429), credentials are not evaluated, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN an authenticated non-staff seller, shelter, or dog lover
WHEN they request any admin URL
THEN access is denied (redirect to login or 403) and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

URL config asserts admin is not at `/admin/`.

### Integration

Staff vs non-staff access tests; admin action produces an audit entry.

### Security

Throttling test on admin login.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-AUTH-003 (rate-limit mechanism)
REQ-AUDIT-001
{{AUTH_MECHANISM}} (staff MFA)
{{ROLE_MATRIX}} (staff permissions)

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

The admin path comes from configuration, not a hard-coded value in the repo. Moving the path is defense in depth only; access control is the real protection.

### Anti-Patterns

- Granting `is_staff` to seller or shelter accounts
- Relying on the hidden path instead of access checks

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
