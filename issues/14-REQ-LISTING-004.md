<!-- title: [REQ-LISTING-004] User-entered listing text is always rendered as text, never as markup -->
<!-- labels: requirement -->

### Requirement ID

REQ-LISTING-004

### Title

User-entered listing text is always rendered as text, never as markup

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

The React client MUST render all user-entered listing text through React's default escaping.
The React client MUST NOT pass user content to `dangerouslySetInnerHTML`.
The API MUST treat listing text as untrusted data and MUST NOT render it as HTML.

### Rationale

Implements SECURITY.md > Input and output (untrusted listing text).

### Applies To

In scope: every React component that displays listing or store text.
Out of scope: rich-text formatting (not planned).

### Components

React client, Django REST API

### Actors

Anonymous visitor, Dog lover (authenticated), Dog seller, Shelter

### Data Classification

Public (published listings)

### Defense Layer

Input validation / output encoding

### Threats Addressed

Stored XSS in listing text, leading to session riding or credential phishing against visitors and other sellers.

### Trust Boundary

Browser -> Django REST API

### Zero Trust Consideration

Content from any seller is untrusted for every other viewer, including the seller's own later sessions.

### OWASP ASVS

V5.3.3, V5.2.1 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

SI-10, SI-15

### NIST SP 800-207

N/A

### Regulatory

UNKNOWN

### Other

OWASP Top 10 2021 - A03 Injection

### AC-01 - Positive case

GIVEN a listing whose text contains plain prose
WHEN it is rendered in the listing view
THEN the text displays as entered

### AC-02 - Invalid / malicious input is rejected

GIVEN a listing whose text contains `<script>`, `<img onerror>`, and `javascript:` payloads
WHEN it is rendered in every listing view
THEN the payloads display as inert text and no script executes

### AC-03 - Negative case

GIVEN the React source tree
WHEN the client test suite runs
THEN it fails if any component passes user content to `dangerouslySetInnerHTML`

### Additional criteria (AC-04+)

_No response_

### On Invalid Input

Reject with 400/422 and field-level errors. Do not partially apply changes. Do not echo raw input back unescaped.

### On System Error (fail closed)

Fail closed. Deny the operation, roll back the transaction, and return a generic 5xx with no stack trace, SQL, or internal paths.

### Alerting

_No response_

### Unit

Component render tests with hostile payloads.

### Integration

End-to-end render of a stored hostile listing.

### Security

XSS payload corpus; source check for `dangerouslySetInnerHTML` until a JS linter is chosen (AGENTS.md: TO BE DECIDED).

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

REQ-LISTING-003
REQ-CLIENT-001

### Downstream

- REQ-EPIC-001

### External

N/A

### Constraints

Any future need for rich text must be raised as a new requirement with a named sanitizer.

### Anti-Patterns

- `dangerouslySetInnerHTML` with user content
- Building HTML strings from listing fields
- User-controlled `href` values without scheme validation

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
