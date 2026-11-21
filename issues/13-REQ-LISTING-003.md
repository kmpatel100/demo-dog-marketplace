<!-- title: [REQ-LISTING-003] Anyone can browse public listings through a paginated read-only API -->
<!-- labels: requirement -->

### Requirement ID

REQ-LISTING-003

### Title

Anyone can browse public listings through a paginated read-only API

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

Functional

### Description (RFC 2119)

The system MUST let anonymous visitors list and view publicly visible listings.
The listing endpoint MUST be paginated and MUST cap the page size.
Public responses MUST include only fields classified as public.
Public listing endpoints MUST NOT accept state-changing methods.

### Rationale

Dog lovers browsing listings is a primary use case in ARCHITECTURE.md. Pagination and field limits implement SECURITY.md > Input and output.

### Applies To

In scope: GET list and GET detail for listings.
Out of scope: search and filtering criteria (TO BE DECIDED), which listing states are public (TO BE DECIDED).

### Components

Django REST API, RDBMS

### Actors

Anonymous visitor, Dog lover (authenticated)

### Data Classification

Public (published listings)

### Defense Layer

Authorization (object-level / role), Business logic

### Threats Addressed

Excessive data exposure (seller PII in public responses); scraping or resource exhaustion through unbounded page sizes.

### Trust Boundary

Browser -> Django REST API, Django REST API -> RDBMS

### Zero Trust Consideration

Public responses use an explicit public field allowlist; non-public listings are filtered server-side.

### OWASP ASVS

V4.1.3, V8.3.4, V11.1.4 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-3, AC-21

### NIST SP 800-207

N/A

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API3:2023 Broken Object Property Level Authorization; API4:2023 Unrestricted Resource Consumption

### AC-01 - Positive case

GIVEN published listings exist
WHEN an anonymous visitor requests the listing endpoint
THEN they receive one page of public listings with only public fields

### AC-02 - Invalid / malicious input is rejected

GIVEN a page size above the cap or a malformed page parameter
WHEN the listing endpoint is requested
THEN the request is rejected (400) or clamped to the cap, and no internal detail is leaked

### AC-03 - Negative case

GIVEN a listing in a non-public state
WHEN an anonymous visitor requests it by ID
THEN access is denied (404) and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Public field allowlist.

### Integration

Pagination, page size cap, and non-public filtering against a real test database.

### Security

Assert no PII fields appear in public responses.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-LISTING-001
REQ-AUTHZ-001
Page size cap value: TO BE DECIDED
Public listing states: TO BE DECIDED
{{PII_INVENTORY}} (which owner fields may be public)

### Downstream

- REQ-EPIC-001
- REQ-LISTING-004
- REQ-CLIENT-001

### External

N/A

### Constraints

Use the Django ORM only.

### Anti-Patterns

- Returning the full model to anonymous callers
- Unbounded `?page_size=`

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
