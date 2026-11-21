<!-- title: [REQ-AUTH-001] Users can register an account with server-assigned roles -->
<!-- labels: requirement -->

### Requirement ID

REQ-AUTH-001

### Title

Users can register an account with server-assigned roles

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

The system MUST create accounts with Django's built-in user model and password hashing.
The system MUST enforce Django's password validators on registration.
The system MUST NOT let the client set role, staff, superuser, or active flags.
The system MUST store passwords only as salted hashes.

### Rationale

Implements SECURITY.md > Authentication and sessions and Input and output (mass assignment). Sellers and shelters need accounts to manage stores and listings.

### Applies To

In scope: registration endpoint.
Out of scope: which actors may self-register and how a seller or shelter role is granted ({{ROLE_MATRIX}}); profile fields ({{PII_INVENTORY}}).

### Components

Django REST API, RDBMS

### Actors

Anonymous visitor

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Authentication / session, Input validation / output encoding

### Threats Addressed

Privilege escalation via mass assignment of role or is_staff; weak passwords; plaintext password storage.

### Trust Boundary

Browser -> Django REST API, Django REST API -> RDBMS

### Zero Trust Consideration

Role and privilege flags are assigned server-side only. Any such field in the request body is rejected.

### OWASP ASVS

V2.1.1, V2.4.1, V5.1.2 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

IA-5(1), AC-2

### NIST SP 800-207

Tenet 6 - authentication and authorization are dynamic and strictly enforced

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API3:2023 Broken Object Property Level Authorization

### AC-01 - Positive case

GIVEN an anonymous visitor with a valid registration payload
WHEN they submit it
THEN an account is created and the stored password is a Django hash, not the plaintext

### AC-02 - Invalid / malicious input is rejected

GIVEN a registration payload containing `role`, `is_staff`, `is_superuser`, or an unknown field
WHEN it is submitted
THEN the request is rejected (400), no account is created, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN a password that fails Django's password validators
WHEN it is submitted
THEN the request is rejected with a field-level error and no account is created

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Registration validator rejects disallowed fields and weak passwords.

### Integration

API tests against a real test database covering AC-01..AC-03.

### Security

Mass-assignment cases for every privilege field.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{ROLE_MATRIX}} must be resolved
{{AUTH_MECHANISM}}
{{PII_INVENTORY}} for registration fields
REQ-AUTH-003 (rate limiting)

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

Use Django's built-in auth (SECURITY.md). No custom password storage.

### Anti-Patterns

- Serializers or forms using all model fields
- Accepting role from the request body

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
