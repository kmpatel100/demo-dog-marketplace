<!-- title: [REQ-CLIENT-002] Sellers and shelters can manage their listings in the React client -->
<!-- labels: requirement -->

### Requirement ID

REQ-CLIENT-002

### Title

Sellers and shelters can manage their listings in the React client

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

The React client MUST let an authenticated seller or shelter create, edit, and delete their own listings.
The React client MUST show only the signed-in account's listings in the management view.
The React client MUST display field-level validation errors returned by the API.
The React client MUST NOT be the only place authorization is enforced.

### Rationale

Store and listing management is a primary use case in ARCHITECTURE.md.

### Applies To

In scope: listing management screens.
Out of scope: store profile screens (follow-up once store fields are decided), photo upload UI (REQ-MEDIA-001).

### Components

React client

### Actors

Dog seller, Shelter

### Data Classification

Public (published listings)

### Defense Layer

Authentication / session, Input validation / output encoding

### Threats Addressed

UI-only authorization bypassed by direct API calls; CSRF on write actions.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

UI hides actions the user cannot take, but every action is re-authorized by the API (REQ-LISTING-002).

### OWASP ASVS

V4.1.1 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

N/A

### NIST SP 800-207

N/A

### Regulatory

UNKNOWN

### Other

N/A

### AC-01 - Positive case

GIVEN an authenticated seller
WHEN they create, edit, and delete a listing in the UI
THEN each action succeeds and the list reflects it

### AC-02 - Invalid / malicious input is rejected

GIVEN an authenticated seller
WHEN they submit a listing form the API rejects with field errors
THEN the client shows the field-level messages and does not show the listing as saved

### AC-03 - Negative case

GIVEN an anonymous visitor or authenticated dog lover
WHEN they open a listing management URL
THEN access is denied: the client redirects to login or shows a not-authorized view, and no management data is fetched

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Display the API's field-level errors. Never render raw API error bodies.

### On System Error (fail closed)

Show a generic error state and keep the user's unsaved input. Do not retry writes automatically.

### Alerting

_No response_

### Unit

Form and error display component tests.

### Integration

Client against a mocked API covering AC-01..AC-03.

### Security

Server-side enforcement is covered by REQ-LISTING-002.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-LISTING-001
REQ-LISTING-002
REQ-AUTH-002
REQ-HTTP-001

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

Session or token handling in the client depends on {{AUTH_MECHANISM}} and {{ORIGIN_TOPOLOGY}}.

### Anti-Patterns

- Storing auth tokens in localStorage
- Treating hidden buttons as access control

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
