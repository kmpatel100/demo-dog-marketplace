<!-- title: [REQ-AUTH-004] Password reset does not reveal accounts and uses single-use expiring tokens -->
<!-- labels: requirement -->

### Requirement ID

REQ-AUTH-004

### Title

Password reset does not reveal accounts and uses single-use expiring tokens

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

The password-reset request endpoint MUST return the same response whether or not the account exists.
Reset tokens MUST be single-use and MUST expire.
A successful reset MUST invalidate the user's existing sessions.
Reset links MUST be built from a configured base URL, not from the request Host header.

### Rationale

Implements SECURITY.md > Authentication and sessions (non-enumerating password reset).

### Applies To

In scope: reset request and reset confirm endpoints.
Out of scope: email provider selection (UNKNOWN).

### Components

Django REST API, External service (specify in Dependencies)

### Actors

Anonymous visitor

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Authentication / session

### Threats Addressed

Account enumeration; reset token replay; host-header poisoning of reset links; account takeover.

### Trust Boundary

Browser -> Django REST API, Django REST API -> external service

### Zero Trust Consideration

The reset token is the only proof of mailbox control; it is verified server-side and consumed on use.

### OWASP ASVS

V2.5.6, V3.3.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

IA-5

### NIST SP 800-207

Tenet 6 - authentication and authorization are dynamic and strictly enforced

### Regulatory

UNKNOWN

### Other

OWASP WSTG-IDNT-04 (account enumeration)

### AC-01 - Positive case

GIVEN a registered user with a valid reset token
WHEN they submit a new valid password
THEN the password changes and all their existing sessions are invalidated

### AC-02 - Invalid / malicious input is rejected

GIVEN a used, expired, or tampered reset token
WHEN it is submitted
THEN the request is rejected (400), the password is unchanged, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN an email address with no account and one with an account
WHEN a reset is requested for each
THEN both return the same status and body, and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Token generator expiry and single-use behaviour.

### Integration

Reset flow with a test email backend.

### Security

Host-header injection test on reset link generation.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-AUTH-002
REQ-AUTH-003 (rate limiting)
Email delivery provider: UNKNOWN

### Downstream

- REQ-EPIC-001

### External

Transactional email service: UNKNOWN

### Constraints

Use Django's built-in password-reset token generator.

### Anti-Patterns

- "No account with that email" messages
- Long-lived or reusable reset tokens

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
