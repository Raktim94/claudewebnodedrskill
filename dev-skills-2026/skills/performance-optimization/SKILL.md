---
name: performance-optimization
description: Make websites, apps, APIs, and databases fast. Use this skill whenever the user mentions slowness, lag, load time, Core Web Vitals, Lighthouse scores, bundle size, memory issues, slow queries, high server costs, or asks to "optimize", "speed up", or "make it faster" — and proactively when building anything user-facing so performance is right from the start.
---

# Performance Optimization — Measure First, Then Fix

The senior-dev rule: **never optimize without measuring**. Profile, find the actual bottleneck, fix the biggest one, re-measure. Guessing wastes time and often makes code worse.

## Step 1: Measure

- Frontend: Lighthouse (lab) + real-user Core Web Vitals. Chrome DevTools Performance panel for traces; Coverage tab for unused JS/CSS; `source-map-explorer`/`webpack-bundle-analyzer` for bundles.
- Backend: add timing to endpoints (p50/p95/p99 — averages lie), APM or OpenTelemetry traces, flame graphs for CPU (`clinic.js`, `py-spy`, `pprof`).
- Database: slow-query log, `EXPLAIN ANALYZE` on suspects, `pg_stat_statements` for total-time offenders.
- State targets: LCP < 2.5s, INP < 200ms, CLS < 0.1, API p95 < 300ms — then optimize until met, then stop.

## Frontend: Core Web Vitals playbook

**LCP (loading)**
- Serve images as AVIF/WebP, sized correctly, `fetchpriority="high"` on the hero, lazy-load everything below the fold (`loading="lazy"`).
- Preload critical assets: `<link rel="preload">` for hero image/font; `rel="preconnect"` for critical origins.
- Fonts: `font-display: swap`, subset, self-host, max 2 families. Variable fonts over multiple weights.
- SSR/SSG for content pages; CDN in front of everything; cache HTML where possible.

**INP (interactivity)**
- Ship less JavaScript — it's the #1 lever. Code-split by route, dynamic-import heavy components (charts, editors, maps), tree-shake, drop heavy deps (moment→date-fns/Temporal, lodash→native).
- Break long tasks (>50ms) with `scheduler.yield()`/`requestIdleCallback`; move heavy computation to Web Workers.
- Debounce inputs; virtualize long lists (react-window/TanStack Virtual) beyond ~100 items.

**CLS (stability)**
- Reserve space: width/height or `aspect-ratio` on all images/embeds/ads; skeletons matching final size; never inject content above existing content.

**Rendering**
- Animate only `transform` and `opacity` (compositor-only). `content-visibility: auto` for long pages. Avoid layout thrashing (batch DOM reads then writes).

## Backend playbook

1. **Fix N+1 queries first** — the most common backend killer. Detect via query logs (same query, many times per request); fix with JOINs, `IN` batching, or dataloader.
2. **Index correctly**: FKs, WHERE/ORDER BY columns, composite indexes matching query patterns (equality columns first, then range). Confirm usage with EXPLAIN; drop unused indexes (they slow writes).
3. **Cache in layers**: HTTP caching headers (`Cache-Control`, ETags) → CDN → Redis for hot objects (cache-aside, TTL + explicit invalidation on write) → in-process memoization. Cache invalidation strategy is part of the design, not an afterthought.
4. **Move slow work off the request path**: email, image processing, exports, webhooks → background queue; respond immediately.
5. **Pagination always** — cursor-based (keyset) for large/infinite lists; never `OFFSET 100000`. Cap page sizes.
6. **Connection pooling** (pgbouncer or driver pool) — creating connections per request destroys throughput.
7. **Payload discipline**: gzip/brotli on, select only needed columns, avoid serializing giant object graphs.

## Database extras

- `SELECT *` is a smell; fetch needed columns.
- Batch writes (multi-row INSERT, `COPY` for bulk).
- For read-heavy analytics on the OLTP database, use a replica or ClickHouse — don't melt production.
- Watch for lock contention: keep transactions short, index foreign keys used in deletes.

## Cost = performance

Slow code is expensive code. After optimizing, note the infra savings (fewer instances, smaller DB tier). Right-size before scaling out.

## Output format

When optimizing, ALWAYS report: (1) what was measured and the baseline numbers, (2) the top bottlenecks found, ranked, (3) fixes applied with code, (4) after numbers or expected impact, (5) the next bottleneck if targets still unmet. Show before/after — unverifiable "should be faster" claims are junior work.
