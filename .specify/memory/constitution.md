# Service Constitution — User Authentication Service (S-01)

This constitution defines quality principles, testing expectations, performance goals, and decision guardrails for the User Authentication Service within the Task Management System.

## Purpose
Provide secure user login, registration, and password reset, issuing JWTs for stateless session management and integrating with platform observability and adjacent services.

## Quality Principles
- Security-first by default
  - Enforce HTTPS for all communications.
  - Issue and validate JWTs; never log secrets, tokens, or passwords.
  - Follow OWASP ASVS for input validation and auth flows.
- Separation of concerns
  - Layered design: controller → service → repository.
  - DTOs isolate external payloads from domain models.
- Reliability and resiliency
  - Apply timeouts/retries and circuit breakers on outbound HTTP.
  - Prefer idempotent operations where applicable (e.g., reset initiation).
- Consistency and traceability
  - Use structured logs with correlation IDs.
  - Consistent error model (ErrorResponse) for 4xx/5xx.
- Evolvability
  - Backward-compatible API changes; semver for API versioning.
  - Feature flags for risky changes (if applicable).

## Testing Principles
- Unit tests for service and repository logic.
- API tests for all endpoints (happy-path, validation, and error paths).
- Contract tests for any outbound integrations (Task Management API, Directory API when used).
- Security tests: authentication/authorization flows, token validation, input sanitization.
- Resiliency tests for timeouts, retries, and circuit breaker behavior.

## Performance and UX Principles
- Fast feedback on auth flows; minimize synchronous external dependencies in login/register.
- Predictable error messages without revealing sensitive details.
- Non-functional targets (indicative; refine with SRE):
  - Median and P90 response latency targets for /login and /register: TODO agree with platform SLOs.
  - Error budgets and rate-limit thresholds: TODO align with platform policies.

## Guardrails for Technical Decisions
- Runtime and Language
  - Node.js with Express per architecture context.
  - Stateless service instances to support horizontal scaling.
- Data
  - MongoDB as the authoritative store for user and auth records (per C2).
  - Unique index on email; store timestamps; password storage must be hashed + salted (algorithm and params: TODO).
- Authentication and Tokens
  - JWT tokens signed with keys from a secure secrets store.
  - Authorization header: Bearer <token>.
  - Token lifetimes, refresh strategy, and revocation: TODO define and align with platform standards.
- Integrations
  - Logging & Monitoring Service: emit structured logs for all critical paths.
  - Message Broker: optional; topics and payloads require explicit design approval (TODO).
  - External Directory API and Task Management API: define strict client policies (timeouts, retries, CB).
  - Advanced User Security Service handles RBAC; do not embed role logic here.
- Compliance and Privacy
  - Handle PII with care; apply data minimization.
  - Follow GDPR-compliant data handling where applicable (from HLD).
  - Delete or redact sensitive data from logs and metrics.

## Delivery and Operations
- Continuous delivery with automated tests and scans.
- Observability:
  - Centralized logs; basic auth flow metrics; distributed tracing hooks.
- Incident response
  - Clear error codes and operational runbooks for investigation.
  - Rollback procedures and feature flag disablement paths.