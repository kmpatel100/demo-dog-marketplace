<!-- title: [REQ-LISTING-002] Sellers and shelters can modify only their own listings -->
<!-- labels: requirement -->

### Requirement ID

REQ-LISTING-002

### Title

Sellers and shelters can modify only their own listings

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

The system MUST allow a listing to be updated or deleted only by its owner.
The system MUST resolve ownership from the authenticated session, not from the request.
The system MUST NOT allow the owner field to be changed through the API.
Denied modification attempts MUST be logged.

### Rationale

Implements SECURITY.md > Authorization (per-object ownership).

### Applies To

In scope: PUT/PATCH/DELETE on a single listing.
Out of scope: public listing search (REQ-LISTING-003); staff moderation ({{ROLE_MATRIX}}).

### Components

Django REST API, RDBMS

### Actors

Dog seller, Shelter

### Data Classification

Public (published listings)

### Defense Layer

Authorization (object-level / role)

### Threats Addressed

IDOR: seller A edits or deletes seller B's listing by changing the {id} path parameter.

### Trust Boundary

Browser -> Django REST API, Django REST API -> RDBMS

### Zero Trust Consideration

Ownership is resolved server-side from the authenticated session. The owner_id in the request body is ignored and rejected.

### OWASP ASVS

V4.2.1, V4.1.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-3, AC-6

### NIST SP 800-207

Tenet 3 - access is granted per session

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API1:2023 Broken Object Level Authorization

### AC-01 - Positive case

GIVEN the owner of a listing
WHEN they update and then delete it
THEN both succeed

### AC-02 - Invalid / malicious input is rejected

GIVEN the owner of a listing
WHEN they send an update that includes `owner` or an unknown field
THEN the request is rejected (400), no state changes, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN a seller or shelter who does not own the listing
WHEN they send PUT, PATCH, or DELETE for it
THEN access is denied (403/404), the listing is unchanged, and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Object permission check.

### Integration

Cross-account update/delete tests for seller-to-seller, shelter-to-seller, and seller-to-shelter.

### Security

IDOR cases with sequential and guessed IDs.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-LISTING-001
REQ-AUTHZ-001

### Downstream

- REQ-EPIC-001
- REQ-MEDIA-001
- REQ-CLIENT-002

### External

N/A

### Constraints

Scope every listing query for writes by owner on the server.

### Anti-Patterns

- Trusting owner_id / role from the request body
- Authorization checks only in the React UI
- Returning stack traces or DB errors to clients

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
