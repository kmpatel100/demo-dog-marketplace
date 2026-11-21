<!-- title: [REQ-LISTING-001] Sellers and shelters can create listings owned by their account -->
<!-- labels: requirement -->

### Requirement ID

REQ-LISTING-001

### Title

Sellers and shelters can create listings owned by their account

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

Functional

### Description (RFC 2119)

The system MUST let an authenticated seller or shelter create a dog listing.
The system MUST set the listing owner from the authenticated session.
The system MUST validate listing fields against an explicit allowlist.
The system MUST NOT let any other actor create listings.

### Rationale

Marketplace listings are a primary use case in ARCHITECTURE.md (assumption A3: sellers and shelters both create listings).

### Applies To

In scope: POST listing endpoint.
Out of scope: listing fields and lifecycle states (TO BE DECIDED), photos (REQ-MEDIA-001), sale vs adoption semantics (UNKNOWN), payments ({{PAYMENTS}}).

### Components

Django REST API, RDBMS

### Actors

Dog seller, Shelter

### Data Classification

Public (published listings)

### Defense Layer

Authorization (object-level / role), Input validation / output encoding

### Threats Addressed

Mass assignment of owner or status; listings created on behalf of another account; oversized payloads.

### Trust Boundary

Browser -> Django REST API, Django REST API -> RDBMS

### Zero Trust Consideration

Owner and status are set server-side. The caller's role is read from the server, never from the request.

### OWASP ASVS

V4.1.3, V5.1.2, V5.1.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-3, SI-10

### NIST SP 800-207

Tenet 6 - authentication and authorization are dynamic and strictly enforced

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API3:2023 Broken Object Property Level Authorization

### AC-01 - Positive case

GIVEN an authenticated seller (and separately a shelter) with a valid listing payload
WHEN they create a listing
THEN it is created with the owner set to their account

### AC-02 - Invalid / malicious input is rejected

GIVEN a listing payload with `owner`, `status`, an unknown field, or an oversized text field
WHEN it is submitted
THEN the request is rejected (400), no listing is created, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN an authenticated dog lover or an anonymous visitor
WHEN they attempt to create a listing
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

Listing validator field allowlist and length limits.

### Integration

API tests against a real test database covering AC-01..AC-03 for both seller and shelter.

### Security

Mass-assignment and oversized-input cases.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-AUTHZ-001
REQ-AUTH-002
{{ROLE_MATRIX}} (confirms A3)
Listing fields and lifecycle states: TO BE DECIDED

### Downstream

- REQ-EPIC-001
- REQ-LISTING-002
- REQ-LISTING-003
- REQ-CLIENT-002

### External

N/A

### Constraints

Use the Django ORM only. Owner is always the requesting account.

### Anti-Patterns

- Serializers or forms using all model fields
- Role checks based on a client-sent flag

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
