# Python backend anti-pattern catalog

Stable IDs support comparison across runs. Entries describe evidence and context that prevents false positives. Groups: database (DB), API, authentication (AUTH), storage (FILE), deployment (OPS), observability/jobs (OBS).

## Database and transactions

- **DB01 — Blocking DB calls in async code.** Trace synchronous SQLAlchemy/PyMySQL calls reached directly from `async def`, including synchronous helpers. FastAPI offloads ordinary `def` path operations/dependencies, not arbitrary helpers. Prefer synchronous request paths or explicit offloading. Exclude awaited async drivers and thread offloading.
- **DB02 — Shared mutable Session.** Find a concrete `Session` reused concurrently. Global engines/session factories are normal. Use a session per request/unit of work; verify scoped-session lifecycle.
- **DB03 — Repository commits fragment business operations.** Trace helper `commit()` calls within operations that should atomically update multiple records. Put transaction ownership at the application-service/unit-of-work boundary. A helper owning an entire independent operation is not automatically wrong.
- **DB04 — Unbudgeted connection capacity.** Sum `replicas × workers × (pool_size + max_overflow)` across services, allowing other consumers and rollout overlap. Check pool-specific unlimited settings. Flag evidenced budget excess or unlimited behavior; unknown server capacity needs verification.
- **DB05 — Blind transaction/request retries.** Inspect retries for side effects/idempotency. Pre-ping checks stale connections at checkout; it cannot rescue a lost transaction. Retry complete operations designed for safe replay with bounded attempts and rollback; blindly replaying POSTs may duplicate side effects.
- **DB06 — Slow work inside transactions.** Trace SMTP, external APIs, CPU/image work, and waits while transactions hold resources. Prepare outside transactions where semantics permit; use an outbox for side effects tied to committed state. Verify when transactions actually begin.
- **DB07 — N+1 during serialization.** Trace response field access to lazy relationships, loading strategies, and collection sizes. Prefer eager/batched loading and query-count verification for important paths. Returning an ORM instance alone does not prove N+1.

## API boundaries

- **API01 — ORM objects define public contracts.** Check arbitrary field exposure without DTO/response-model filtering, especially user/auth/audit/admin data. Explicit response schemas can safely filter returned ORM instances.
- **API02 — Unbounded collections.** Trace list queries/materialization; enforce pagination with server-side page-size maximums. Check defaults and intentionally bounded collections.
- **API03 — Validation substitutes for authorization.** Trace principal-to-object ownership/permissions on reads/writes, including dependencies and query filters. Valid IDs/DTOs confer no access; intentionally public data may be appropriate.

## Authentication

- **AUTH01 — Single-dimension email-code throttling.** Inspect source/IP and normalized target email/account limits, including shared state. Rotating IPs defeats source-only limits; cycling addresses defeats target-only limits. Report concrete missing layers.
- **AUTH02 — Account enumeration.** Compare public auth/recovery responses, status codes, and observable paths for existing/absent accounts. Use consistent responses. Timing risk needs evidence or measurement; authenticated admin lookups differ.
- **AUTH03 — Plaintext or indefinitely retained login codes.** Inspect storage protection, expiry enforcement, consumption, and retention. Require short validity; prefer a protected representation appropriate for low-entropy codes. Never reproduce actual values.
- **AUTH04 — Non-atomic refresh rotation.** Trace validate/consume/replace under concurrency. Use transactional conditional updates or locking ensuring one consumption. A transaction without concurrency-safe conditions is insufficient.
- **AUTH05 — Excessively long access-token validity.** Evaluate lifetime against threat model, revocation, and refresh design. Multi-day bearer tokens can undermine rotation/session control. Avoid universal lifetime thresholds.
- **AUTH06 — Token-controlled JWT algorithms.** Inspect accepted algorithm/key configuration. Algorithms must be configured server-side, not chosen solely from untrusted headers. Constrained header-based key selection is not automatically insecure.
- **AUTH07 — Secrets in logs.** Trace middleware, application/debug logs, and configuration for Authorization, access/refresh tokens, verification codes, Cookie/Set-Cookie, SMTP passwords. Check redaction and production reachability. Structured logs do not protect secrets. Cite paths/keys with values redacted.

## Uploads and storage

- **FILE01 — Trusting filenames/MIME headers.** Client Content-Type/extensions alone cannot validate images. Inspect actual decoding/validation and derive safe stored representations. Check filename/path use.
- **FILE02 — Image decoding without limits.** Trace compressed-byte limits, dimensions/pixels, decompression-bomb handling, and rejection before full allocation. HTTP size limits alone cannot bound expanded memory.
- **FILE03 — BLOBs loaded for metadata queries.** Inspect selected columns on actual list paths. Defer/separate large columns or use projections/dedicated endpoints. A BLOB column's presence alone proves nothing.
- **FILE04 — Unexamined BLOB growth.** Seek measured/evidenced storage, replication, backup, restore, and cache pressure. Recommend measurement/object storage where warranted. BLOB use alone is not a defect; unknown scale needs verification.

## Deployment and migrations

- **OPS01 — Every replica runs migrations.** Trace production entrypoints/deployment steps. Concurrent per-replica DDL risks races; prefer a separate controlled step. Account for effective locking/single-run orchestration.
- **OPS02 — Destructive migrations break overlapping versions.** Inspect drops/renames/data changes and callers against rollout/rollback order. Prefer staged expansion, migration, contraction. Historical destructive migrations are not automatically current risks.
- **OPS03 — SQLite-only integration tests for MariaDB.** Inspect fixtures, CI, and migration checks for actual production-family DB coverage. SQLite differs in syntax, types, constraints, collation, and locking. MariaDB Testcontainers is a safeguard; skipped coverage needs context.
- **OPS04 — Production reload.** Find `--reload`/reload settings in reachable production commands. Development reload is legitimate.
- **OPS05 — Unconsidered process/replica capacity.** Check hosting topology, redundancy, and workload evidence. One worker can be intentional in replicated containers. A lone Compose process may warrant benchmarks/resilience review; one worker alone is not a defect.
- **OPS06 — Workers scale without pool recalculation.** Check worker/replica changes against DB04's budget; independent processes normally have independent pools. Combine findings sharing DB04's root cause, keeping both IDs.
- **OPS07 — Expensive health probes.** Inspect cadence, queries, external calls, and time bounds. Prefer cheap bounded checks with separate liveness/readiness. A simple bounded readiness DB ping is not automatically expensive.
- **OPS08 — Dependency outages trigger restarts.** Connect actual restart policies to dependency checks. Liveness typically checks process/event-loop health; readiness checks dependencies. Readiness failure or Compose health status alone does not prove restart behavior.

## Observability and background jobs

- **OBS01 — High-cardinality metrics.** Trace labels with user IDs, email, arbitrary URL paths, or other unbounded values. Prefer route templates, method, status, and controlled dimensions. Verify raw versus normalized paths.
- **OBS02 — Process-local tasks for durable jobs.** Trace FastAPI BackgroundTasks or similar work used for must-deliver email/required side effects. Crashes/redeploys can lose jobs. Use a durable queue or outbox/job table with safe retries; modest best-effort work may be appropriate.
