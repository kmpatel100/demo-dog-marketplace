<!-- title: [REQ-PLATFORM-003] API errors return generic responses with no internal detail -->
<!-- labels: requirement -->

### Requirement ID

REQ-PLATFORM-003

### Title

API errors return generic responses with no internal detail

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

The API MUST return a generic error body for every 5xx response.
Error responses MUST NOT contain stack traces, SQL, file paths, or framework version details.
On an unhandled exception the API MUST roll back the current transaction.
Validation errors (4xx) MUST use a consistent JSON shape with field-level messages.

### Rationale

Implements SECURITY.md > Logging and errors. Gives every other requirement a shared fail-closed error path.

### Applies To

In scope: global exception handling for all API endpoints.
Out of scope: React error UI.

### Components

Django REST API

### Actors

Anonymous visitor, Dog lover (authenticated), Dog seller, Shelter

### Data Classification

Internal

### Defense Layer

Logging / monitoring / error handling

### Threats Addressed

Information disclosure via verbose errors used for reconnaissance or SQL injection tuning.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

Every caller, authenticated or not, receives the same minimal error detail.

### OWASP ASVS

V7.4.1, V7.4.2 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

SI-11

### NIST SP 800-207

N/A

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API8:2023 Security Misconfiguration

### AC-01 - Positive case

GIVEN a request with invalid fields
WHEN it is sent to any write endpoint
THEN the API returns 400/422 with the shared JSON error shape and field-level messages

### AC-02 - Invalid / malicious input is rejected

GIVEN a view that raises an unhandled exception after a database write
WHEN it is called
THEN the API returns a generic 5xx body, the write is rolled back, and the body contains no stack trace, SQL, or path

### AC-03 - Negative case

GIVEN a request to an unknown route or a disallowed method
WHEN it is sent
THEN the API returns 404/405 with the generic error shape and no framework debug output

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

Log the exception server-side with a correlation ID returned to the client (see REQ-AUDIT-001). Alerting channel is TO BE DECIDED.

### Unit

Exception handler maps exceptions to the generic shape.

### Integration

Fault-injection view verifies rollback and response body.

### Security

Assert response bodies never match stack-trace, SQL, or path patterns.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REST library choice (e.g. Django REST Framework vs plain Django views): TO BE DECIDED

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

Must hold with DEBUG = False (REQ-PLATFORM-001).

### Anti-Patterns

- Returning `str(exception)` to the client
- Swallowing exceptions without rollback

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
