<!-- title: [REQ-EPIC-001] EPIC: Dog marketplace MVP (stores, listings, accounts) -->
<!-- labels: requirement -->

### Requirement ID

REQ-EPIC-001

### Title

EPIC: Dog marketplace MVP (stores, listings, accounts)

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

The system MUST satisfy every child requirement listed under Upstream before this epic is marked Verified.
The system MUST NOT be released to production while any P0 child is below Verified.
Child requirements MUST NOT resolve UNKNOWN or TO BE DECIDED values by assumption.
Each blocked child MUST name the decision it waits on under Upstream.

### Rationale

Delivers the system purpose in ARCHITECTURE.md: sellers and shelters manage stores and marketplace listings, and dog lovers browse them, on a React client and Django REST API backed by an RDBMS, under the rules in SECURITY.md.

### Applies To

In scope:
- Platform hardening and error handling (REQ-PLATFORM-001..003, REQ-HTTP-001)
- Accounts and sessions (REQ-AUTH-001..004)
- Authorization baseline (REQ-AUTHZ-001)
- Store management (REQ-STORE-001)
- Listings (REQ-LISTING-001..004)
- Dog photos, blocked (REQ-MEDIA-001)
- Admin, audit logging, supply chain (REQ-ADMIN-001, REQ-AUDIT-001, REQ-SUPPLY-001)
- React client (REQ-CLIENT-001..002)

Out of scope until decided (no child issues filed):
- Payments ({{PAYMENTS}})
- Personal data retention and deletion ({{PII_INVENTORY}})
- Compliance obligations ({{COMPLIANCE}})
- Deployment, migrations as a deploy step, HSTS ({{DEPLOYMENT_PLATFORM}})
- Scale targets (ARCHITECTURE.md: TO BE DECIDED)
- Vulnerability disclosure ({{SECURITY_CONTACT}})

### Components

React client, Django REST API, RDBMS, Django admin, CI/CD (TO BE DECIDED), Deployment / infrastructure (TO BE DECIDED), External service (specify in Dependencies)

### Actors

Dog seller, Shelter, Dog lover (authenticated), Anonymous visitor, Staff / admin, System / background job

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Network / edge (TLS, HSTS), HTTP boundary (CORS, CSRF, headers), Authentication / session, Authorization (object-level / role), Input validation / output encoding, Business logic, Data storage, Secrets management, Logging / monitoring / error handling, Supply chain / CI-CD

### Threats Addressed

Aggregate of child issues: IDOR, mass assignment, CSRF, stored XSS in listing text, credential stuffing, account enumeration, secret leakage, malicious uploads, vulnerable dependencies.

### Trust Boundary

Browser -> Django REST API, Django REST API -> RDBMS, Django REST API -> external service, Staff -> Django admin, CI/CD -> deployment environment

### Zero Trust Consideration

Every request is authenticated and authorized server-side, least privilege is the default, and client-supplied identity, role, or ownership is never trusted. Each child states its specific control.

### OWASP ASVS

ASVS 4.0.3: see child issues

### OWASP AISVS

N/A

### NIST SP 800-53

See child issues

### NIST SP 800-207

Tenets 2, 3, 5, 6, 7 (see child issues)

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 2023: API1, API2, API3, API4, API5, API8 (see child issues)

### AC-01 - Positive case

GIVEN every child requirement is in Verified status
WHEN the full automated test suite runs
THEN every child's AC tests pass and branch coverage is at least 80%

### AC-02 - Invalid / malicious input is rejected

GIVEN the security regression tests from all children (IDOR, mass assignment, CSRF, injection, XSS, oversized input)
WHEN they run against the API
THEN every hostile request is rejected (4xx) with no state change and no internal detail in the response

### AC-03 - Negative case

GIVEN the full list of API routes
WHEN the REQ-AUTHZ-001 route audit runs
THEN every route has an explicit permission, unauthorized access is denied (401/403/404), and the attempt is logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

Per child issue. Alerting channel is TO BE DECIDED.

### Unit

Per child issue.

### Integration

Per child issue, against a real test database.

### Security

Aggregate security regression suite built from child issues.

### Compliance

N/A until {{COMPLIANCE}} is resolved.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

Child requirements:
- [ ] REQ-PLATFORM-001: Production settings are hardened and pass Django's deploy check
- [ ] REQ-PLATFORM-002: Secrets are loaded from the environment and never committed or bundled
- [ ] REQ-PLATFORM-003: API errors return generic responses with no internal detail
- [ ] REQ-HTTP-001: Cross-origin and CSRF protections match the client/API origin topology
- [ ] REQ-AUTH-001: Users can register an account with server-assigned roles
- [ ] REQ-AUTH-002: Login and logout manage sessions securely
- [ ] REQ-AUTH-003: Authentication endpoints are rate-limited
- [ ] REQ-AUTH-004: Password reset does not reveal accounts and uses single-use expiring tokens
- [ ] REQ-AUTHZ-001: Every API endpoint denies access unless a permission is explicitly declared
- [ ] REQ-STORE-001: Sellers can create and update only their own store
- [ ] REQ-LISTING-001: Sellers and shelters can create listings owned by their account
- [ ] REQ-LISTING-002: Sellers and shelters can modify only their own listings
- [ ] REQ-LISTING-003: Anyone can browse public listings through a paginated read-only API
- [ ] REQ-LISTING-004: User-entered listing text is always rendered as text, never as markup
- [ ] REQ-MEDIA-001: Listing owners can upload dog photos safely
- [ ] REQ-ADMIN-001: Django admin is restricted to staff and hardened
- [ ] REQ-AUDIT-001: Security events are logged without sensitive data
- [ ] REQ-SUPPLY-001: Dependencies are pinned and scanned for vulnerabilities and secrets
- [ ] REQ-CLIENT-001: Visitors can browse and view listings in the React client
- [ ] REQ-CLIENT-002: Sellers and shelters can manage their listings in the React client

Blocking decisions (resolve before the dependent children move past Draft):
- {{AUTH_MECHANISM}}, {{ORIGIN_TOPOLOGY}}, {{ROLE_MATRIX}}, {{FILE_UPLOADS}}, {{PII_INVENTORY}}, {{DEPLOYMENT_PLATFORM}}, {{CI_CD_SYSTEM}} (SECURITY.md)
- REST library choice (e.g. Django REST Framework vs plain Django views): TO BE DECIDED
- Store and listing data fields, listing lifecycle states, sale vs adoption semantics: TO BE DECIDED
- Python/JS linters, formatters, test runners, package managers: TO BE DECIDED (AGENTS.md)
- Transactional email provider for password reset: UNKNOWN
- RDBMS engine: UNKNOWN (ARCHITECTURE.md)

### Downstream

_No response_

### External

Email provider, media storage, secret store, CI system: all UNKNOWN or TO BE DECIDED

### Constraints

Stack is limited to what ARCHITECTURE.md lists. Each child is implemented and merged independently and referenced by its REQ ID.

### Anti-Patterns

- Implementing a child before its blocking decisions are resolved
- Bundling several children into one PR
- Adding frameworks or services not in ARCHITECTURE.md

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Work one child issue at a time. Do not implement this epic directly.
- Raise UNKNOWN items instead of assuming them.
- Tick a child here only when it reaches Verified.
