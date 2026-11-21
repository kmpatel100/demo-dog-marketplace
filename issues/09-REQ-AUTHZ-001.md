<!-- title: [REQ-AUTHZ-001] Every API endpoint denies access unless a permission is explicitly declared -->
<!-- labels: requirement -->

### Requirement ID

REQ-AUTHZ-001

### Title

Every API endpoint denies access unless a permission is explicitly declared

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

The API MUST deny access to any endpoint that does not explicitly declare its permission policy.
The API MUST enforce authorization on the server for every request.
Unauthenticated users MUST have read-only access to public listing data only.
Every authorization denial MUST be logged.

### Rationale

Implements SECURITY.md > Authorization (deny by default, server-side enforcement).

### Applies To

In scope: default permission policy and a route-level audit for all API endpoints.
Out of scope: per-object ownership rules (REQ-STORE-001, REQ-LISTING-002).

### Components

Django REST API

### Actors

Anonymous visitor, Dog lover (authenticated), Dog seller, Shelter, Staff / admin

### Data Classification

Confidential (PII such as seller/shelter contact details)

### Defense Layer

Authorization (object-level / role)

### Threats Addressed

Broken function-level authorization: a new endpoint shipped without a permission check becomes publicly writable.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

No endpoint is reachable by default. Access is granted only by an explicit, reviewed permission declaration.

### OWASP ASVS

V4.1.1, V4.1.3, V4.1.5 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-3, AC-6

### NIST SP 800-207

Tenet 6 - authentication and authorization are dynamic and strictly enforced

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API5:2023 Broken Function Level Authorization

### AC-01 - Positive case

GIVEN an endpoint that declares a public read permission
WHEN an anonymous visitor sends GET
THEN the request succeeds

### AC-02 - Invalid / malicious input is rejected

GIVEN an anonymous visitor
WHEN they send POST, PUT, PATCH, or DELETE to any endpoint
THEN the request is rejected (401/403), no state changes, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN the full list of API routes
WHEN the route audit test runs
THEN every route has an explicit permission declaration, and an endpoint without one is denied (401/403) and the denial is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

Log each denial with user ID (or anonymous), endpoint, and method. Alerting channel is TO BE DECIDED.

### Unit

Default permission policy is deny.

### Integration

Anonymous write attempts against every write route.

### Security

Automated route enumeration that fails CI on any route lacking an explicit permission.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{ROLE_MATRIX}} for role-level permissions
REST library choice (e.g. Django REST Framework vs plain Django views): TO BE DECIDED

### Downstream

- REQ-EPIC-001
- REQ-STORE-001
- REQ-LISTING-001
- REQ-LISTING-002
- REQ-LISTING-003

### External

N/A

### Constraints

Client-side checks are UX only (SECURITY.md).

### Anti-Patterns

- Global `AllowAny` default
- Authorization checks only in the React UI

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
