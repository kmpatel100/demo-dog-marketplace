<!-- title: [REQ-MEDIA-001] Listing owners can upload dog photos safely -->
<!-- labels: requirement -->

### Requirement ID

REQ-MEDIA-001

### Title

Listing owners can upload dog photos safely

### Version

0.1.0

### Status

Draft

### Author

UNKNOWN

### Last Updated

2026-11-21

### Priority

P2 - Medium

### Classification

Security

### Description (RFC 2119)

The system MUST validate uploaded photo content type and size against an allowlist.
The system MUST store uploaded files where they cannot be executed.
The system MUST allow photos to be attached only to listings the uploader owns.
The system MUST NOT use client-supplied filenames as storage paths.

### Rationale

Implements SECURITY.md > Input and output (file uploads). Blocked until {{FILE_UPLOADS}} confirms uploads exist and sets limits.

### Applies To

In scope: photo upload and attach endpoints.
Out of scope: allowed types, size limits, and storage location (all {{FILE_UPLOADS}}).

### Components

Django REST API, React client, External service (specify in Dependencies)

### Actors

Dog seller, Shelter

### Data Classification

Public (published listings)

### Defense Layer

Input validation / output encoding, Data storage, Authorization (object-level / role)

### Threats Addressed

Malicious file upload (HTML/SVG/script served as content); path traversal via filenames; storage exhaustion; attaching photos to another owner's listing.

### Trust Boundary

Browser -> Django REST API, Django REST API -> external service

### Zero Trust Consideration

File content is verified server-side; the declared Content-Type and filename are never trusted.

### OWASP ASVS

V12.1.1, V12.2.1, V12.3.1, V12.5.2 (ASVS 4.0.3)

### OWASP AISVS

N/A

### NIST SP 800-53

SI-10, SI-3

### NIST SP 800-207

N/A

### Regulatory

UNKNOWN

### Other

OWASP API Security Top 10 - API4:2023 Unrestricted Resource Consumption

### AC-01 - Positive case

GIVEN the owner of a listing with an allowed image under the size limit
WHEN they upload it
THEN it is stored under a server-generated name and attached to the listing

### AC-02 - Invalid / malicious input is rejected

GIVEN a file whose content is not an allowed image type, is over the size limit, or has a traversal filename
WHEN it is uploaded
THEN the request is rejected (400/413), nothing is stored, and the response leaks no internal detail

### AC-03 - Negative case

GIVEN a seller who does not own the listing
WHEN they upload a photo to it
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

Content sniffing and size validation.

### Integration

Upload flow against the chosen storage backend.

### Security

Polyglot, SVG-with-script, and traversal filename cases.

### Compliance

Map each cited control ID to the test(s) that verify it in the PR description.

### Coverage

- [X] Code implementing this requirement MUST reach >= 80% branch coverage.
- [X] Every acceptance criterion has at least one automated test.

### Upstream

{{FILE_UPLOADS}} must be resolved
REQ-LISTING-002
Media storage: UNKNOWN (ARCHITECTURE.md)

### Downstream

- REQ-EPIC-001

### External

Media storage backend: UNKNOWN

### Constraints

Do not start implementation until {{FILE_UPLOADS}} is resolved.

### Anti-Patterns

- Trusting the browser's Content-Type
- Serving uploads from the application origin with an executable content type

### AI Development Guidance

- Follow AGENTS.md, ARCHITECTURE.md, and SECURITY.md.
- Implement only what this requirement states. Raise UNKNOWN items instead of assuming them.
- Write tests for AC-01..AC-03 before or alongside the implementation.
- Do not introduce new dependencies, secrets, or endpoints without stating them here.
