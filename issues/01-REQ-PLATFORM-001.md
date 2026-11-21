<!-- title: [REQ-PLATFORM-001] Production settings are hardened and pass Django's deploy check -->
<!-- labels: requirement -->

### Requirement ID

REQ-PLATFORM-001

### Title

Production settings are hardened and pass Django's deploy check

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

The Django API MUST run with DEBUG = False in production.
ALLOWED_HOSTS MUST be an explicit list and MUST NOT contain "*".
SecurityMiddleware, CsrfViewMiddleware, and XFrameOptionsMiddleware MUST be enabled.
`python manage.py check --deploy` MUST report no warnings before any production release, except HSTS settings deferred until {{DEPLOYMENT_PLATFORM}} is decided.
The system MUST serve all traffic over HTTPS only.

### Rationale

Implements SECURITY.md > HTTP boundary and Supply chain and deployment (`check --deploy`). Debug pages and wildcard hosts leak internals and enable host-header attacks.

### Applies To

In scope: Django production settings module and the release checklist.
Out of scope: TLS termination and HSTS values (blocked on {{DEPLOYMENT_PLATFORM}}).

### Components

Django REST API, Deployment / infrastructure (TO BE DECIDED)

### Actors

System / background job

### Data Classification

Internal

### Defense Layer

Network / edge (TLS, HSTS), HTTP boundary (CORS, CSRF, headers)

### Threats Addressed

Information disclosure via debug pages; Host-header poisoning (e.g. password-reset link poisoning); clickjacking; cleartext transport.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

The Host header is untrusted input and is validated against an explicit allowlist. No request is trusted because it arrives on an internal network.

### OWASP ASVS

V14.3.2, V14.4.7, V9.1.1 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

CM-6, CM-7, SC-8

### NIST SP 800-207

Tenet 2 - all communication is secured regardless of network location

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API8:2023 Security Misconfiguration

### AC-01 - Positive case

GIVEN the production settings module
WHEN `python manage.py check --deploy` runs in CI
THEN it exits 0 with no warnings other than the documented HSTS deferral

### AC-02 - Invalid / malicious input is rejected

GIVEN the API running with production settings
WHEN a request arrives with a Host header not in ALLOWED_HOSTS
THEN the request is rejected (400), no view code runs, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN a settings module with DEBUG = True, ALLOWED_HOSTS containing "*", or a required middleware removed
WHEN the settings test suite runs
THEN the test fails and blocks the merge

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Settings assertions: DEBUG is False, ALLOWED_HOSTS has no wildcard, required middleware are present.

### Integration

Request with a foreign Host header returns 400.

### Security

`check --deploy` in CI once {{CI_CD_SYSTEM}} exists; until then, run locally and record output in the PR.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{DEPLOYMENT_PLATFORM}} for HTTPS/HSTS values
{{CI_CD_SYSTEM}} to automate the deploy check

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

Production values come from environment variables (see REQ-PLATFORM-002). Local setup commands are UNKNOWN (AGENTS.md).

### Anti-Patterns

- `ALLOWED_HOSTS = ['*']`
- Toggling DEBUG from a committed default of True
- Disabling middleware to silence a failing check

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
