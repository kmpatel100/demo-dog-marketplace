<!-- title: [REQ-CLIENT-001] Visitors can browse and view listings in the React client -->
<!-- labels: requirement -->

### Requirement ID

REQ-CLIENT-001

### Title

Visitors can browse and view listings in the React client

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

The React client MUST display a paginated list of public listings from the REST API.
The React client MUST display a listing detail view.
The React client MUST communicate with the server only through the REST API.
The React client MUST show a generic message when the API returns an error.

### Rationale

Dog lovers browsing listings is a primary use case in ARCHITECTURE.md.

### Applies To

In scope: listing list and detail pages.
Out of scope: search and filter UI (TO BE DECIDED), visual design (TO BE DECIDED).

### Components

React client

### Actors

Anonymous visitor, Dog lover (authenticated)

### Data Classification

Public (published listings)

### Defense Layer

Input validation / output encoding

### Threats Addressed

XSS through listing text (handled by REQ-LISTING-004); leaking internal error detail into the UI.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

The client displays only what the API returns; it holds no secrets and makes no authorization decisions.

### OWASP ASVS

V5.3.3 (ASVS 4.0.3)

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

GIVEN the API returns a page of listings
WHEN a visitor opens the listings page and pages forward
THEN each page renders its listings and the detail view opens for a selected listing

### AC-02 - Invalid / malicious input is rejected

GIVEN the API returns a malformed response or a 5xx
WHEN the listings page loads
THEN the client shows a generic error message and renders no raw response content

### AC-03 - Negative case

GIVEN a listing ID that the API reports as not found
WHEN a visitor opens its detail URL
THEN the client shows a not-found view with no listing data

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Show a generic, user-friendly message. Never render raw API error bodies.

### On System Error (fail closed)

Show a generic error state. Do not retry writes automatically.

### Alerting

_No response_

### Unit

Component tests for list, pagination, detail, and error states.

### Integration

Client against a mocked API covering AC-01..AC-03.

### Security

Covered by REQ-LISTING-004.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-LISTING-003
JS test runner: TO BE DECIDED
React build tooling: TO BE DECIDED

### Downstream

- REQ-EPIC-001
- REQ-LISTING-004

### External

N/A

### Constraints

Origin and API base URL handling depend on {{ORIGIN_TOPOLOGY}}.

### Anti-Patterns

- Calling the database or any non-REST backend from the client
- Embedding API keys in the bundle

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
