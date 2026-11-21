<!-- title: [REQ-HTTP-001] Cross-origin and CSRF protections match the client/API origin topology -->
<!-- labels: requirement -->

### Requirement ID

REQ-HTTP-001

### Title

Cross-origin and CSRF protections match the client/API origin topology

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

Every state-changing request (POST, PUT, PATCH, DELETE) MUST be CSRF-protected when cookie sessions are used, including login.
GET requests MUST NOT change state.
If the client and API are on different origins, the API MUST use a CORS allowlist of exact origins.
The API MUST NOT combine `Access-Control-Allow-Origin: *` with credentials.

### Rationale

Implements SECURITY.md > HTTP boundary. The exact CORS, CSRF, and cookie settings depend on assumption A1 in ARCHITECTURE.md.

### Applies To

In scope: CORS and CSRF configuration for every API endpoint.
Out of scope: authentication flow itself (REQ-AUTH-002).

### Components

Django REST API, React client

### Actors

Anonymous visitor, Dog lover (authenticated), Dog seller, Shelter

### Data Classification

Confidential (PII such as seller/shelter contact details)

### Defense Layer

HTTP boundary (CORS, CSRF, headers)

### Threats Addressed

CSRF against seller or shelter accounts; login CSRF; cross-origin data theft through a permissive CORS policy.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

The browser's Origin is verified against an explicit allowlist on every request; no origin is trusted by default.

### OWASP ASVS

V4.2.2, V14.5.3, V13.2.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

SC-23, AC-4

### NIST SP 800-207

Tenet 3 - access is granted per session

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API8:2023 Security Misconfiguration

### AC-01 - Positive case

GIVEN an authenticated session and a valid CSRF token from an allowed origin
WHEN a state-changing request is sent
THEN it succeeds

### AC-02 - Invalid / malicious input is rejected

GIVEN an authenticated session
WHEN a state-changing request is sent without a CSRF token or with an invalid one
THEN the request is rejected (403), no state changes, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN a request from an origin not on the allowlist
WHEN it is sent with credentials
THEN no CORS allow headers are returned for that origin and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Configuration assertions: allowlist has no wildcard, credentials never paired with `*`.

### Integration

CSRF-missing and foreign-origin requests against a representative write endpoint.

### Security

Route enumeration test: no GET handler performs a write.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{ORIGIN_TOPOLOGY}} must be resolved
{{AUTH_MECHANISM}} (cookie sessions vs tokens changes the CSRF model)

### Downstream

- REQ-EPIC-001
- REQ-AUTH-002
- REQ-CLIENT-002

### External

N/A

### Constraints

Adding a CORS library is a new dependency and must be stated here before use.

### Anti-Patterns

- `CORS_ALLOW_ALL_ORIGINS = True`
- `@csrf_exempt` on state-changing views
- State changes in GET handlers

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
