<!-- title: [REQ-AUDIT-001] Security events are logged without sensitive data -->
<!-- labels: requirement -->

### Requirement ID

REQ-AUDIT-001

### Title

Security events are logged without sensitive data

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

The system MUST log login success, login failure, permission denials, and admin actions.
Each security log entry MUST include timestamp, event type, user ID (or anonymous), and endpoint.
The system MUST NOT log passwords, session IDs, tokens, or request bodies that contain personal data.

### Rationale

Implements SECURITY.md > Logging and errors. Several requirements' AC-03 depends on this logging.

### Applies To

In scope: security event logger and its use from auth, authorization, and admin code.
Out of scope: log storage, retention, and alerting destination (TO BE DECIDED with {{DEPLOYMENT_PLATFORM}}).

### Components

Django REST API, Django admin

### Actors

System / background job

### Data Classification

Confidential (PII such as seller/shelter contact details)

### Defense Layer

Logging / monitoring / error handling

### Threats Addressed

Undetected credential stuffing or IDOR probing; credential leakage through logs.

### Trust Boundary

N/A

### Zero Trust Consideration

Every access decision produces evidence, so policy can be monitored and improved.

### OWASP ASVS

V7.1.1, V7.1.2, V7.2.1, V7.2.2 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AU-2, AU-3, AU-9

### NIST SP 800-207

Tenet 7 - collect information to improve security posture

### Regulatory

UNKNOWN

### Other

OWASP Top 10 2021 - A09 Security Logging and Monitoring Failures

### AC-01 - Positive case

GIVEN a successful login, a failed login, a permission denial, and an admin change
WHEN each occurs
THEN one structured log entry per event is written with the required fields

### AC-02 - Invalid / malicious input is rejected

GIVEN a failed login whose request body contains a password, or a request carrying a session cookie
WHEN the event is logged
THEN the entry contains no password, session ID, token, or raw request body

### AC-03 - Negative case

GIVEN an authenticated user denied access to another user's listing
WHEN the denial occurs
THEN access is denied (403/404) and a denial entry is logged with user ID, endpoint, and object ID

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

Alerting channel is TO BE DECIDED.

### Unit

Log formatter strips sensitive keys.

### Integration

Captured-log assertions for each event type.

### Security

Scan captured logs in the test run for password, session, and token patterns.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

Log sink and retention: TO BE DECIDED
{{PII_INVENTORY}} (which fields count as personal data)

### Downstream

- REQ-EPIC-001
- REQ-ADMIN-001

### External

N/A

### Constraints

Use Python's standard logging; adding a logging service is new infrastructure and needs an ARCHITECTURE.md change.

### Anti-Patterns

- Logging full request bodies
- Logging raw cookies or Authorization headers

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
