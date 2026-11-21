<!-- title: [REQ-SUPPLY-001] Dependencies are pinned and scanned for vulnerabilities and secrets -->
<!-- labels: requirement -->

### Requirement ID

REQ-SUPPLY-001

### Title

Dependencies are pinned and scanned for vulnerabilities and secrets

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

Security

### Description (RFC 2119)

Python and JS dependencies MUST be pinned with committed lockfiles.
CI MUST run dependency vulnerability scanning and secret scanning on every PR.
CI jobs triggered from forks or PRs MUST NOT receive production secrets.
CI logs MUST NOT echo secrets.

### Rationale

Implements SECURITY.md > Supply chain and deployment.

### Applies To

In scope: lockfiles for both stacks and CI security checks.
Out of scope: deployment and migration steps (deferred until {{DEPLOYMENT_PLATFORM}}).

### Components

CI/CD (TO BE DECIDED), Django REST API, React client

### Actors

System / background job

### Data Classification

Restricted (credentials, secrets, payment data)

### Defense Layer

Supply chain / CI-CD, Secrets management

### Threats Addressed

Vulnerable or hijacked dependency versions; secrets committed to the repo; secret exfiltration from fork-triggered CI.

### Trust Boundary

CI/CD -> deployment environment

### Zero Trust Consideration

Untrusted contributions run in CI without access to production secrets.

### OWASP ASVS

V14.2.1, V14.2.4 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

RA-5, SR-3, SA-11

### NIST SP 800-207

Tenet 5 - monitor and measure the integrity and security posture of assets

### Regulatory

UNKNOWN

### Other

OWASP Top 10 2021 - A06 Vulnerable and Outdated Components

### AC-01 - Positive case

GIVEN the repository
WHEN dependencies are installed from lockfiles in a clean environment
THEN installed versions exactly match the lockfiles

### AC-02 - Invalid / malicious input is rejected

GIVEN a PR that adds a dependency with a known critical vulnerability or commits a test secret
WHEN CI runs
THEN the check fails and the PR cannot merge

### AC-03 - Negative case

GIVEN a PR opened from a fork
WHEN CI runs
THEN no production secret is available to the job, and any attempt to read one is denied and logged

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

N/A

### Integration

CI pipeline run on seeded vulnerable-dependency and secret fixtures.

### Security

Dependency and secret scanners in CI.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{CI_CD_SYSTEM}} must be resolved
Python and JS package managers: TO BE DECIDED

### Downstream

- REQ-EPIC-001

### External

Scanning tools: TO BE DECIDED with {{CI_CD_SYSTEM}}

### Constraints

Lockfiles can be added now; scanning waits for {{CI_CD_SYSTEM}}.

### Anti-Patterns

- Unpinned `>=` ranges without a lockfile
- Exposing production secrets to `pull_request` jobs from forks

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
