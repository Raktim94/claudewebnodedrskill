---
name: backend-api-design
description: Design and build production-grade backends and APIs — REST/GraphQL/tRPC design, validation, error handling, auth, database access, background jobs, logging, and deployment readiness. Use this skill whenever the user builds any server, API, endpoint, webhook, microservice, or backend feature in Node/Python/Go/etc., or asks how to structure a backend — even for small projects, so the result is senior-quality.
---

# Backend & API Design — Production-Grade by Default

Build backends that another engineer could operate at 3am: predictable APIs, explicit errors, observable behavior, no surprises.

## API design (REST defaults)

- Resource-oriented URLs: plural nouns, no verbs. `GET /orders/123`, `POST /orders`, `PATCH /orders/123`, `DELETE /orders/123`. Actions that don't fit CRUD: `POST /orders/123/cancel`.
- Correct status codes: 200/201 (created, return the resource)/204 (no body), 400 validation, 401 unauthenticated, 403 unauthorized, 404, 409 conflict, 422 semantic errors, 429 rate-limited, 5xx server faults only.
- **Consistent error envelope** (RFC 9457 problem details or equivalent) on every non-2xx:

```json
{ "type": "validation_error", "title": "Invalid request", "status": 400,
  "errors": [{ "field": "email", "message": "must be a valid email" }],
  "request_id": "req_8fk2..." }
```

Never leak stack traces or SQL in responses. Never return 200 with `{"error": ...}`.

- Version from day one: `/v1/...` (or header). Additive changes are free; breaking changes need a new version + deprecation window.
- Pagination on every list endpoint: cursor-based (`?cursor=...&limit=50`, return `next_cursor`), max limit enforced. Filtering/sorting via documented query params only.
- Idempotency keys (`Idempotency-Key` header) on payment/creation endpoints so retries are safe.
- Choose the protocol deliberately: REST for public APIs, tRPC for TS monorepos, GraphQL only when diverse clients need flexible queries (and then add depth/complexity limits), WebSocket/SSE for realtime, webhooks (signed, retried, idempotent) for outbound events.

## Request lifecycle (every endpoint)

1. **Authenticate** (middleware) → 2. **Authorize** against the specific resource (ownership/tenant check — IDOR is the #1 API vuln) → 3. **Validate** the full input with a schema (Zod/Pydantic); reject unknown fields → 4. **Execute** business logic in a service layer (thin controllers) → 5. **Respond** with a typed serializer — explicit field allowlist, never dump ORM models (mass-exposure of password hashes/internal fields is a classic bug).

## Database access

- Transactions around multi-step writes; keep them short.
- Kill N+1s (JOIN/dataloader); index FKs and query columns; use `EXPLAIN` when unsure.
- Migrations: versioned, reversible-or-forward-safe, backward compatible with running code (expand → migrate → contract).
- Connection pooling always. Timeouts on queries.
- Money = integer cents or `DECIMAL`. Time = UTC `timestamptz` everywhere, convert at the edge. IDs = UUIDv7/bigint.

## Reliability

- Timeouts on every outbound call; retries with exponential backoff + jitter for idempotent operations only; circuit-break flaky dependencies.
- Slow work (email, media, exports, third-party syncs) goes to a background queue with retry + dead-letter; the request path stays fast.
- Rate limiting (per-key/user/IP) with `429` + `Retry-After`.
- Graceful shutdown: stop accepting, drain in-flight, close pools. Health endpoints: `/healthz` (liveness) and `/readyz` (deps up).

## Observability

- Structured JSON logs with `request_id` (generate at ingress, propagate everywhere incl. jobs) — log requests, errors with stack, and key business events; never log secrets/tokens/PII.
- Metrics: request rate, error rate, p50/p95/p99 latency per endpoint (RED). Alert on symptoms (error rate, latency), not causes.
- OpenTelemetry tracing when there's more than one service.

## Configuration & security

- 12-factor config: env vars, validated at boot with a schema, crash fast when missing. Secrets from a secret manager in prod.
- Apply the cybersecurity skill for auth, headers, and input handling — they compose.

## Documentation & testing

- OpenAPI spec generated from code (or code from spec) — never hand-drift. Include auth, error shapes, and examples.
- Integration tests against a real database (testcontainers) for every endpoint: happy path + authz failure + validation failure. Unit tests for business logic. Contract tests if other teams consume the API.

## Output format

When building: brief design (resources, endpoints table: method | path | auth | purpose) → schema/migrations → complete code with validation + error handling + logging (no elided sections) → example requests/responses incl. an error case → how to run and test.
