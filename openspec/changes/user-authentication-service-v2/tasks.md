# User Authentication Service (S-01) — Implementation Tasks

## Core Domain and Data Models
- [ ] Define User model (id: UUID string, email, createdAt) — align with LLD.
- [ ] Define DTOs: LoginRequest, RegisterRequest, ResetPasswordRequest; ResponseModel, ErrorResponse.
- [ ] Add validation rules (email format, password min length 6).

## HTTP Endpoints (Express)
- [ ] Implement POST /api/v1/auth/login in UserController.postLogin.
- [ ] Implement POST /api/v1/auth/register in UserController.postRegister.
- [ ] Implement POST /api/v1/auth/reset-password in UserController.resetPassword.
- [ ] Implement GET /health liveness route.

## Application Services
- [ ] Implement UserService.login(email, password): lookup, verify, issue JWT, log outcome.
- [ ] Implement UserService.register(dto): validate, persist in MongoDB, log outcome.
- [ ] Implement UserService.resetPassword(email): verify user exists, initiate email flow (TBD), log outcome.

## Repositories and Persistence
- [ ] Implement UserRepository.save(user).
- [ ] Implement UserRepository.findById(id).
- [ ] Implement UserRepository.findByEmail(email) — implied by login/reset flows.
- [ ] Configure MongoDB connection, indexes on email (unique), createdAt.

## Integration Clients / External Calls
- [ ] Create TaskManagementApiClient with base URL, timeouts, retries, circuit breaker (exact endpoints/payloads: TODO).
- [ ] Create DirectoryApiClient (optional) for External User Directory API; add resilience (timeouts/retries/CB). Define mappings: TODO.

## Security and Token Management
- [ ] Implement JWT issuance and verification utilities (signing keys from secrets).
- [ ] Ensure Authorization: Bearer token handling documented for consumers.
- [ ] Avoid sensitive data in logs; mask emails where appropriate.

## Resiliency and Reliability
- [ ] Add request timeouts and retry policies for outbound HTTP clients.
- [ ] Add circuit breaker around external calls (Task Management API, Directory API).
- [ ] Add rate limiting for /api/v1/auth/* endpoints (platform-level or service-level).

## Observability
- [ ] Emit structured logs for login/register/reset successes and failures.
- [ ] Add basic metrics (request counts, latency, error rates) if available.
- [ ] Include correlation IDs in logs if provided upstream.

## Message Broker (Optional/TBD)
- [ ] Define topics/queues for auth-related events (e.g., user.registered, auth.failed) — TODO.
- [ ] Implement publisher for relevant events — TODO.
- [ ] Implement consumer if any inbound events are required — TODO.

## Error Handling
- [ ] Centralize error mapping to ErrorResponse with correct HTTP status codes.
- [ ] Add input validation error handling (400), auth errors (401), not found (404), and unexpected errors (500).

## Configuration and Secrets
- [ ] Wire environment variables/secrets for MongoDB URI, JWT keys, external API creds.
- [ ] Document configuration defaults and required env vars.

## Tests
- [ ] Unit tests for UserService (login/register/reset).
- [ ] Unit tests for UserRepository (save/findByEmail).
- [ ] API tests for endpoints (200/201/400/401/404 paths).
- [ ] Resiliency tests for HTTP clients (timeouts/retries/CB behavior).

## Documentation
- [ ] Update README with API endpoints and example requests/responses.
- [ ] Document operational runbooks for health checks and log inspection.