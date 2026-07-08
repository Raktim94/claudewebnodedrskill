---
name: system-design
description: Senior architect-level system design for scalable software. Use this skill whenever the user asks to design an app/system architecture, choose a database or tech stack, plan for scale, add caching/queues/microservices, design a schema, or asks "how should I structure/build X" for any non-trivial app, backend, or platform — even if they don't say "system design".
---

# System Design — Senior Architect Standards

Design systems the way a staff engineer would: start from requirements, choose boring proven technology, justify every component, and never add complexity without a stated reason.

## Process

1. **Clarify requirements before proposing anything.** Ask or state assumptions for: expected users/traffic (10? 10k? 10M?), read vs write ratio, consistency needs, latency targets, budget, and team size.
2. **Estimate scale.** Back-of-envelope: requests/sec, storage growth/year, peak vs average. Design for ~10x current needs, not 1000x.
3. **Start with the simplest architecture that meets requirements.** A modular monolith + Postgres + one cache serves most products to millions of users. Recommend microservices only when team size (>~20 engineers) or independent scaling truly demands it — and say so explicitly.
4. **Present the design as:** requirements → high-level diagram (Mermaid) → data model → API surface → scaling path → failure modes → trade-offs considered.

## Default stack decisions (2026)

- **Database**: PostgreSQL by default. It handles relational data, JSONB documents, full-text search, vector embeddings (pgvector), and queues (SKIP LOCKED) — don't add MongoDB/Elastic/Pinecone until Postgres measurably can't cope. Choose SQLite/Turso for single-node or edge apps; Redis/Valkey for cache + ephemeral state; ClickHouse for analytics at volume.
- **Communication**: REST/JSON for public APIs, tRPC or gRPC for internal service-to-service, WebSocket/SSE for realtime, webhooks + queues for async.
- **Queue/events**: Start with Postgres-backed jobs (e.g., pg-boss, Sidekiq/BullMQ on Redis). Kafka/NATS only for genuine event-streaming volume or multi-consumer fan-out.
- **Hosting**: Managed platforms first (Vercel/Fly/Railway/Cloud Run/ECS). Kubernetes only with a platform team to run it.

## Data modeling

- Normalize first; denormalize only for measured hot paths.
- Every table: `id` (UUIDv7 or bigint), `created_at`, `updated_at`. Soft-delete only when business requires undo/audit.
- Index every foreign key and every column in frequent WHERE/ORDER BY. Verify with `EXPLAIN ANALYZE`.
- Plan multi-tenancy upfront: `tenant_id` column + composite indexes (+ RLS if security-critical). Retrofitting is painful.
- Migrations must be backward-compatible (expand → migrate data → contract) so deploys are zero-downtime.

## Scaling path (apply in this order, measure between steps)

1. Add indexes and fix N+1 queries (usually 10–100x wins, free)
2. Cache: CDN for static, Redis for hot reads (cache-aside; set TTLs; plan invalidation)
3. Read replicas for read-heavy loads
4. Queue slow work (email, exports, media) into background jobs
5. Vertical scaling — a bigger box is cheaper than distributed complexity
6. Horizontal app scaling behind a load balancer (requires stateless app servers; sessions in Redis/JWT)
7. Only then: partitioning/sharding, CQRS, service extraction

## Reliability & failure design

For every external dependency, state what happens when it's down. Standard toolkit: timeouts on every network call (default nothing to infinity), retries with exponential backoff + jitter (idempotent operations only), circuit breakers on flaky dependencies, idempotency keys on payment/write APIs, dead-letter queues for poison messages, health checks + graceful shutdown. Define SLOs (e.g., p99 < 300ms, 99.9% uptime) so "reliable" is measurable.

## CAP / consistency guidance

State the consistency requirement per feature, not per system: money and inventory need strong consistency (transactions, single writer); feeds, counts, and analytics tolerate eventual consistency. Prefer strongly-consistent single-region Postgres until multi-region is a real requirement.

## Output format

ALWAYS deliver: (1) assumptions & scale estimate, (2) Mermaid architecture diagram, (3) component list with one-line justification each, (4) schema sketch for core entities, (5) the scaling path, (6) top 3 failure modes with mitigations, (7) explicitly rejected alternatives and why. If the user needs the "day-1 version", give a minimal variant alongside the target architecture.
