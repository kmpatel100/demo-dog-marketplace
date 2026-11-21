<!-- title: [REQ-AUTH-003] Authentication endpoints are rate-limited -->
<!-- labels: requirement -->

### Requirement ID

REQ-AUTH-003

### Title

Authentication endpoints are rate-limited

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

The system MUST rate-limit login, registration, and password-reset requests.
Requests over the limit MUST be rejected with 429 and MUST NOT be processed.
Rate-limit responses MUST NOT reveal whether the targeted account exists.

### Rationale

Implements SECURITY.md > Authentication and sessions (rate limiting).

### Applies To

In scope: login, registration, and password-reset endpoints.
Out of scope: Django admin login (REQ-ADMIN-001); thresholds and windows are TO BE DECIDED.

### Components

Django REST API

### Actors

Anonymous visitor

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Authentication / session, Business logic

### Threats Addressed

Credential stuffing; password brute force; registration spam; password-reset email flooding.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

Limits apply per client and per target account, so neither rotating IPs nor rotating accounts bypasses them.

### OWASP ASVS

V2.2.1, V11.1.4 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-7

### NIST SP 800-207

Tenet 7 - collect information to improve security posture

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API4:2023 Unrestricted Resource Consumption

### AC-01 - Positive case

GIVEN a client under the configured limit
WHEN it calls the login endpoint
THEN the request is processed normally

### AC-02 - Invalid / malicious input is rejected

GIVEN a client that has exceeded the configured limit
WHEN it sends another login, registration, or password-reset request
THEN the API returns 429, does not evaluate credentials or send email, and leaks no internal detail

### AC-03 - Negative case

GIVEN repeated failed logins against one account from many clients
WHEN the per-account limit is reached
THEN further attempts are denied (429) and the event is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Limiter keying (per client and per account).

### Integration

Burst tests against each of the three endpoints.

### Security

Verify that throttled requests never reach the credential check.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

Thresholds and windows: TO BE DECIDED
Rate-limit storage backend: UNKNOWN

### Downstream

- REQ-EPIC-001
- REQ-AUTH-001
- REQ-AUTH-002
- REQ-AUTH-004
- REQ-ADMIN-001

### External

Rate-limiting library (if any): TO BE DECIDED. State it here before adding.

### Constraints

Limits must be configurable per environment.

### Anti-Patterns

- Client-side-only throttling
- Limiting by IP only

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
