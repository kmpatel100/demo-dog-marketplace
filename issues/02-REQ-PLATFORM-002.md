<!-- title: [REQ-PLATFORM-002] Secrets are loaded from the environment and never committed or bundled -->
<!-- labels: requirement -->

### Requirement ID

REQ-PLATFORM-002

### Title

Secrets are loaded from the environment and never committed or bundled

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

The system MUST load SECRET_KEY, database credentials, and API keys from the environment or a secret store.
The repository MUST NOT contain secrets, and `.env*` MUST be listed in `.gitignore`.
The React build MUST NOT contain any server secret.
Each environment MUST use its own secret values.
The API MUST refuse to start in production when a required secret is missing.

### Rationale

Implements SECURITY.md > Secrets.

### Applies To

In scope: Django settings, `.gitignore`, React build-time environment variables.
Out of scope: choice of secret store (blocked on {{DEPLOYMENT_PLATFORM}}).

### Components

Django REST API, React client, Deployment / infrastructure (TO BE DECIDED)

### Actors

System / background job

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Secrets management

### Threats Addressed

Credential leakage through git history; secrets shipped in the public JS bundle; cross-environment secret reuse.

### Trust Boundary

CI/CD -> deployment environment

### Zero Trust Consideration

The client bundle is treated as fully public. Secrets are scoped per environment so one leak does not grant access elsewhere.

### OWASP ASVS

V2.10.4, V6.4.1, V14.1.3 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

IA-5, SC-12, SC-28

### NIST SP 800-207

Tenet 6 - authentication and authorization are dynamic and strictly enforced

### Regulatory

UNKNOWN

### Other

N/A

### AC-01 - Positive case

GIVEN all required secrets are set in the environment
WHEN the API starts with production settings
THEN it starts and reads every secret from the environment

### AC-02 - Invalid / malicious input is rejected

GIVEN a required secret (e.g. SECRET_KEY) is missing or empty
WHEN the API starts with production settings
THEN startup fails with an error naming the missing variable but not any secret value

### AC-03 - Negative case

GIVEN a production React build
WHEN the build output is scanned for known server secret names and values
THEN no match is found

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Settings loader raises on missing required variables.

### Integration

Build the React client and grep the bundle for server secret names.

### Security

Secret scanning in CI once {{CI_CD_SYSTEM}} is chosen (see REQ-SUPPLY-001).

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{DEPLOYMENT_PLATFORM}} for the secret store

### Downstream

- REQ-EPIC-001

### External

Secret store: UNKNOWN

### Constraints

Only variables intended to be public may be exposed to the React build.

### Anti-Patterns

- Default SECRET_KEY values in settings
- Committing `.env` files
- Passing server secrets through React build-time variables

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
