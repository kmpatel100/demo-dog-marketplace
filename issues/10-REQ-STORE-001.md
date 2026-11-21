<!-- title: [REQ-STORE-001] Sellers can create and update only their own store -->
<!-- labels: requirement -->

### Requirement ID

REQ-STORE-001

### Title

Sellers can create and update only their own store

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

The system MUST let an authenticated seller create a store owned by their account.
The system MUST set the store owner from the authenticated session.
The system MUST NOT let a user update or delete a store they do not own.
The system MUST reject unknown or protected fields (owner, status) in store payloads.

### Rationale

Store management is a primary use case in ARCHITECTURE.md. Ownership enforcement implements SECURITY.md > Authorization.

### Applies To

In scope: store create, read-own, update, delete endpoints.
Out of scope: store fields (TO BE DECIDED), whether shelters have stores ({{ROLE_MATRIX}}), stores per seller (UNKNOWN).

### Components

Django REST API, RDBMS

### Actors

Dog seller

### Data Classification

Confidential (PII such as seller/shelter contact details)

### Defense Layer

Authorization (object-level / role), Input validation / output encoding

### Threats Addressed

IDOR: seller A edits seller B's store by changing the store ID; mass assignment of owner.

### Trust Boundary

Browser -> Django REST API, Django REST API -> RDBMS

### Zero Trust Consideration

Ownership is resolved server-side from the authenticated session. Any owner value in the request body is rejected.

### OWASP ASVS

V4.2.1, V5.1.2, V5.1.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

AC-3, AC-6, SI-10

### NIST SP 800-207

Tenet 3 - access is granted per session

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API1:2023 Broken Object Level Authorization

### AC-01 - Positive case

GIVEN an authenticated seller
WHEN they create a store and later update it
THEN both succeed and the store owner is their account

### AC-02 - Invalid / malicious input is rejected

GIVEN a store payload containing `owner`, `status`, or an unknown field
WHEN it is submitted
THEN the request is rejected (400), no state changes, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN seller A and a store owned by seller B
WHEN seller A sends PUT, PATCH, or DELETE for seller B's store
THEN access is denied (403/404) and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Store validator field allowlist.

### Integration

API tests against a real test database covering AC-01..AC-03.

### Security

IDOR and mass-assignment cases.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-AUTHZ-001
REQ-AUTH-002
{{ROLE_MATRIX}}
{{PII_INVENTORY}} (store contact fields)
Store data fields: TO BE DECIDED

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

Use the Django ORM only (SECURITY.md).

### Anti-Patterns

- Trusting owner_id from the request body
- Looking up stores by ID without an owner filter

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
