# Security

The single source of security rules for this project. The system they apply to is described in [ARCHITECTURE.md](ARCHITECTURE.md). Rules are provisional until the open decisions below are resolved.

## Rules

### HTTP boundary
- Serve all traffic over HTTPS only. Enable HSTS once `{{DEPLOYMENT_PLATFORM}}` is decided.
- In production, run with `DEBUG = False` and an explicit `ALLOWED_HOSTS` list. Never use a wildcard.
- Keep `SecurityMiddleware`, `CsrfViewMiddleware`, and `XFrameOptionsMiddleware` enabled.
- If the client and API are on different origins, use a CORS allowlist of exact origins. Never combine `*` with credentials.
- When cookie sessions are used, every state-changing request (POST, PUT, PATCH, DELETE) must be CSRF-protected, including login.
- Every state-changing request must be authenticated, except login, registration, and password reset.
- GET requests must never change state.

### Authentication and sessions
- Use Django's built-in auth and password hashing. No custom crypto or password store.
- Set session cookies with `Secure`, `HttpOnly`, and `SameSite=Lax` or stricter.
- Rotate the session on login and invalidate it on logout.
- Rate-limit login, registration, and password-reset endpoints.
- Login and password-reset responses must not reveal whether an account exists.

### Authorization
- Enforce authorization on the server for every endpoint. Client-side checks are for UX only.
- Deny by default: an endpoint is reachable only after an explicit permission is declared.
- Check ownership per object. Sellers and shelters can change only their own store and listings. Never trust an owner ID sent by the client.
- Unauthenticated users get read-only access to public listing data only.
- Restrict Django admin to staff accounts, move it off the default `/admin/` path, and rate-limit it.

### Input and output
- Validate every request body, query parameter, and path parameter on the server against an explicit serializer or form with allowlisted fields. Reject unknown or mass-assigned fields such as owner, role, or status.
- Use the Django ORM or parameterized queries only. Never build SQL from strings containing user input.
- Treat user-entered listing text as untrusted. Rely on React's default escaping; never pass user content to `dangerouslySetInnerHTML`.
- Paginate list endpoints and cap their page size.
- If file uploads exist, validate content type and size, and store files where they cannot be executed.
- If payments exist, card data must never touch our servers.

### Secrets
- Never commit secrets (`SECRET_KEY`, database credentials, API keys). Load them from the environment or a secret store.
- Add `.env*` to `.gitignore` from the first commit.
- Never put server secrets in the React bundle. Everything in the client build is public.
- Use separate secrets per environment.

### Logging and errors
- Return generic error messages to clients. Never return stack traces, SQL, or internal paths.
- Never log passwords, session IDs, tokens, or request bodies containing personal data.
- Log security events: login success and failure, permission denials, and admin actions.

### Supply chain and deployment
- Pin Python and JS dependencies with lockfiles.
- Run dependency vulnerability scanning and secret scanning in CI once `{{CI_CD_SYSTEM}}` is chosen.
- Never echo secrets in CI logs. CI jobs triggered from forks or PRs must not receive production secrets.
- Run database migrations as a controlled deploy step, never automatically from untrusted branches.
- Run `python manage.py check --deploy` before every production release.

## Open Decisions

Each placeholder is UNKNOWN or TO BE DECIDED until a human resolves it. Reference the placeholder in issues that depend on it.

- `{{AUTH_MECHANISM}}`: sessions (assumption A2) vs tokens/JWT, and whether MFA is required for sellers, shelters, or staff.
- `{{ORIGIN_TOPOLOGY}}`: same-origin vs cross-origin client and API (assumption A1). Determines CORS, CSRF, and cookie settings.
- `{{ROLE_MATRIX}}`: permissions for seller, shelter, dog lover (anonymous and authenticated), and staff.
- `{{PAYMENTS}}`: none, or a named hosted provider.
- `{{FILE_UPLOADS}}`: whether dog photos are uploaded, allowed types and size limits, and storage location.
- `{{PII_INVENTORY}}`: personal data collected (e.g. seller and shelter contact details, addresses), retention, and deletion.
- `{{DEPLOYMENT_PLATFORM}}`: secret store and TLS termination, once the deployment model in ARCHITECTURE.md is decided.
- `{{CI_CD_SYSTEM}}`: pipeline tool and where the required checks run.
- `{{COMPLIANCE}}`: applicable regulatory requirements.
- `{{SECURITY_CONTACT}}`: where to report vulnerabilities.
