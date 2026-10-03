# Shared .NET Windows desktop anti-pattern catalog

Read for WinUI/WinForms applications and their shared .NET libraries or Windows services. Read the relevant shell catalog separately. Stable `NET-*` IDs distinguish these entries from Python and shell-specific findings. Search terms locate candidates; trace actual execution, ownership, and safeguards before reporting. A library, singleton, legacy API, or missing preferred abstraction alone is not a defect.

## Contents

- Architecture and dependency injection (`NET-ARCH`)
- Async, threading, and lifetime (`NET-ASYNC`)
- SQLite and persistence (`NET-SQL`)
- Scheduling and background work (`NET-JOB`)
- FTP and remote transfers (`NET-FTP`)
- Privileged services, IPC, and snapshots (`NET-IPC`)
- Cryptography and secrets (`NET-SEC`)
- Configuration (`NET-CFG`)
- Updates and deployment (`NET-UPD`)
- Preview handlers and COM (`NET-COM`)
- Native interop (`NET-NATIVE`)
- Logging and localization (`NET-LOG`, `NET-LOC`)
- Dependencies and build (`NET-BUILD`)
- Filesystem operations (`NET-FS`)

## Architecture and dependency injection

Candidates: form event handlers, ViewModels, `App.Current`, static `Program` fields, `IServiceProvider`, `AddSingleton`, `BuildServiceProvider`.

- **NET-ARCH01 — Shells own divergent business rules.** Trace domain decisions duplicated in forms/pages/ViewModels or shared engine code depending on concrete UI types. Report demonstrated coupling, inconsistent behavior/defaults, or inability to run a required headless path; keep orchestration in shared application operations. Presentation-only differences and small event adapters are legitimate.
- **NET-ARCH02 — Controllers/ViewModels combine unrelated lifecycles.** Inspect objects owning dialogs, persistence, scheduling, IPC, and domain rules together. Show a concrete change/lifetime/error-handling problem before recommending narrower services; size alone is insufficient.
- **NET-ARCH03 — Hidden global dependencies.** Trace static service locators, global mutable state, repeated manual container resolution, or dependencies constructed inside services. Establish conflicting ownership, unreplaceable boundary behavior, or concurrency risk. Composition-root resolution and deliberate factories are normal.
- **NET-ARCH04 — Captive or incorrectly shared dependencies.** Inspect singleton registrations capturing scoped/disposable transient services, mutable ViewModels, windows, DB connections, or network clients. Trace actual retention, disposal, and concurrent callers. Use appropriate scopes/factories and thread-safe ownership; singleton registration alone is not wrong. Scope validation helps detect mistakes but its absence alone proves none.
- **NET-ARCH05 — Duplicate service-provider graphs.** Trace `BuildServiceProvider()` during registration or multiple roots creating duplicate singleton/state/resource owners. Use the intended composition root; intentionally isolated containers need not be merged.
- **NET-ARCH06 — Unobserved initialization or shutdown.** Trace constructors launching async work, host startup without awaited stop/dispose, and shutdown that loses required writes or log flushes. Use explicit initialization and bounded graceful shutdown. Account for unexpected termination; cleanup cannot depend exclusively on orderly close.
- **NET-ARCH07 — Failures reduced to ambiguous bool/string results.** Follow swallowed exceptions or generic error responses that make success/partial failure indistinguishable or erase actionable context. Preserve typed outcomes and diagnostic details without leaking secrets; explicit, complete result types are valid.

## Async, threading, and lifetime

Candidates: `.Result`, `.Wait()`, `GetAwaiter().GetResult()`, `async void`, `_ =`, timers, dispatcher calls, synchronous DB/FTP/WMI/RPC, hashing/preview loops.

- **NET-ASYNC01 — Blocking the UI thread.** Trace synchronous waits, I/O, `Thread.Sleep`, or substantial CPU work reached from the UI thread. Show freeze or a continuation requiring the blocked context. Await real async I/O; move bounded CPU work off-thread where appropriate. `async` alone does not offload work; a synchronously completed task is not automatically a deadlock.
- **NET-ASYNC02 — Unowned async work.** Trace `async void` outside framework event callbacks or discarded tasks lacking failure observation and lifetime ownership. Use Task-returning operations/commands. Event handlers may be `async void`, but must handle failures and coordinate shutdown; intentionally supervised background work is valid.
- **NET-ASYNC03 — Cross-thread UI access.** Trace worker callbacks touching controls, XAML objects, or bound collections whose consumers require UI affinity. Marshal through the owning dispatcher and handle shutdown/dispatch rejection. Background computation of plain data alone is safe.
- **NET-ASYNC04 — Cancellation stops only the UI indication.** Follow tokens through enumeration, transfers, previews, DB work, and IPC; inspect actual abort/cleanup behavior. Report continuing work touching disposed state, retained resources, or misleading cancellation. An API without cooperative cancellation may need a bounded timeout and explicit ownership rather than an invented token parameter.
- **NET-ASYNC05 — Reentrant timers or commands corrupt shared work.** Trace overlapping timer callbacks, scheduler invocations, or enabled command concurrency operating on the same resource. Coordinate by operation/resource and observe failures. Parallel independent operations are valid; avoid replacing all concurrency with a global lock.
- **NET-ASYNC06 — Callbacks outlive their owner.** Inspect subscriptions, tray handlers, timers, navigation, and native callbacks retained after window/service disposal. Verify unsubscription/cancellation and outstanding callbacks before reporting leaks or use-after-dispose.

## SQLite and persistence

Candidates: `SQLiteConnection`, `SQLiteCommand`, `BeginTransaction`, transaction modes, busy handling, concatenated SQL, DB file paths, schema upgrades.

- **NET-SQL01 — Provider objects shared unsafely across threads.** Establish the exact provider/version's contract and actual concurrent or cross-thread connection/command/reader use. Prefer per-operation ownership and disposal. SQLite engine threading modes do not establish managed-provider safety; serialized access or supported provider behavior needs separate verification.
- **NET-SQL02 — Transactions hold locks across slow external work.** Trace transfer, filesystem, RPC, or CPU work while a transaction is active. Inspect provider-specific deferred/immediate behavior and lock acquisition, including read-to-write upgrades. Do not claim every read transaction is a write transaction. Keep lock duration bounded while preserving atomicity.
- **NET-SQL03 — Incorrect contention retries.** Trace expected `SQLITE_BUSY`/locked conditions with no bounded handling, or retries of corruption/schema/programming errors. Retry only replay-safe operations with rollback and bounds; busy timeout alone does not solve conflicting application semantics.
- **NET-SQL04 — Storage violates locking/durability assumptions.** Inspect network/synchronized/removable DB locations, journal/WAL configuration, filesystem locking, and failure modes. Network storage can invalidate SQLite assumptions; removable local storage is not inherently unsupported. Recommend a supported location or coordination based on actual topology.
- **NET-SQL05 — DB commit treated as external-operation commit.** Follow metadata changes and file/remote mutations across failure, crash, and retry. Find orphan data, phantom records, or publication of incomplete work. Use staged state, reconciliation, or an idempotent consistency protocol; a DB transaction cannot roll back arbitrary filesystem effects.
- **NET-SQL06 — Unsafe queries or local secret storage.** Trace untrusted filenames/configuration into concatenated SQL and plaintext credentials in accessible DBs. Parameterize values, allowlist identifiers, and use suitable protected secret storage. Local-only inputs can still be attacker-controlled.
- **NET-SQL07 — Unsafe schema/recovery lifecycle.** Inspect migration versioning, transactional support, concurrent upgrades, rollback compatibility, and recovery from interrupted writes/corruption. Report concrete incompatibility or unrecoverable required state; absence of a routine integrity scan alone is insufficient.

## Scheduling and background work

Candidates: Quartz `RAMJobStore`, persistent stores, job keys, concurrency attributes, misfire policies, cron/time-zone settings, shell/service startup.

- **NET-JOB01 — Required jobs depend on volatile shell state.** Trace in-memory schedules or UI-owned job state expected to survive close/reboot. Verify reconstruction/persistent stores and the intended session lifetime. Session-only work and reliably reconstructed schedules are legitimate.
- **NET-JOB02 — Multiple authorities schedule the same operation.** Inspect shell instances, services, database schedule records, and Quartz jobs for duplicate or stale state after restart/change. Define authoritative state and recoverable synchronization; two stores are not inherently wrong when reconciliation is effective.
- **NET-JOB03 — Concurrent/retried jobs lack resource coordination or idempotency.** Trace duplicate execution and partial progress against shared destinations/state. Check job identity and the actual scope of scheduler concurrency guards. Use resource-specific ownership and replay-safe checkpoints; do not assume an attribute prevents overlap between distinct job keys or processes.
- **NET-JOB04 — Sleep, misfires, DST, and retry semantics are accidental.** Inspect configured misfire behavior, explicit time zones, ambiguous/invalid local times, and job exception policies. Show skipped, duplicated, or burst execution contrary to requirements. Avoid universal cron/time-zone policies.
- **NET-JOB05 — Jobs reach UI objects.** Trace worker execution accessing forms/windows or depending on their lifetime. Route through application operations and publish presentation updates safely; UI-session scheduling may be intentional but still needs affinity/lifetime handling.

## FTP and remote transfers

Candidates: FluentFTP `EncryptionMode`, `ValidateAnyCertificate`, certificate callbacks, remote paths, retries, upload/rename operations.

- **NET-FTP01 — Sensitive transfers permit plaintext or insecure fallback.** Inspect control and data-channel protection and behavior when TLS negotiation fails. Require authenticated encrypted transport for credentials/sensitive content. Verify installed-library semantics; an enum named `Auto` is not proof of fail-closed encryption.
- **NET-FTP02 — Certificate validation is bypassed.** Trace unconditional acceptance (`ValidateAnyCertificate`, callbacks setting `Accept = true`) or disabled checks on reachable production paths. Verify deliberate trust-store/pinning alternatives and actual validation. Development-only overrides are distinct from production trust failures.
- **NET-FTP03 — Untrusted remote paths reach commands unsanitized.** Trace external filenames/paths through the installed library's command escaping/path sanitizer and server-side scope. Reject command delimiters/traversal as appropriate; Windows `Path.Combine` does not implement FTP path semantics. Verify advisories for the installed release instead of hard-coding a version cutoff.
- **NET-FTP04 — Transfer success publishes incomplete state.** Inspect resume/overwrite, cancellation, verification, and partial uploads exposed under final names. Stage and verify before publish, using rename only where server semantics support it. Protocol success alone does not establish independently required remote durability.
- **NET-FTP05 — Remote operations have unbounded lifetime or leak secrets.** Trace timeouts, cancellation, retry limits, raw server logging, and credentials in URLs. Report actual shutdown/resource risks or disclosure; redact diagnostic data.

## Privileged services, IPC, and snapshots

Candidates: named-pipe creation/ACLs, ServiceWire contracts, caller identity/impersonation, `Process.Start`, service accounts, VSS snapshot/freeze/cleanup.

- **NET-IPC01 — Excess service privilege or broad privileged RPC.** Trace account/token capabilities against required operations and methods exposing arbitrary paths, executables, handles, devices, or volumes. Narrow contracts and enforce authorized resource scope server-side. LocalSystem is not automatically wrong when the operation demonstrably requires it.
- **NET-IPC02 — Pipe access is mistaken for operation authorization.** Inspect effective pipe ACLs, intended principals, caller identity/session, and per-operation authorization. Default descriptors and UI validation do not establish a safe boundary. A restrictive ACL can implement authorization for a deliberately single-role contract; show the actual unauthorized capability before reporting.
- **NET-IPC03 — Privileged service trusts caller-controlled paths/configuration.** Trace canonicalization, reparse-point/TOCTOU behavior, low-privilege writable configuration, and ownership checks under the service identity. Treat inputs as untrusted even from a local client; see NET-FS01 for the same underlying path race.
- **NET-IPC04 — Disconnect/retry duplicates or abandons privileged work.** Trace request IDs, cancellation, broken-pipe recovery, resource cleanup, time bounds, and graceful service stop. Use operation status/idempotency and recover abandoned resources. Client disconnect does not always require aborting durable work, but ownership must remain clear.
- **NET-IPC05 — Snapshot consistency failures are hidden.** For VSS or similar mechanisms, trace writer/provider failures, timeout/freeze-window work, cleanup, and reported consistency level. Surface degraded guarantees; keep expensive processing outside constrained freeze windows. A snapshot's existence alone does not prove application consistency.
- **NET-IPC06 — RPC contracts expose implementation lifecycles.** Inspect native handles/objects or shared implementation dependencies crossing process/version boundaries. Report unsafe ownership, broad attack surface, or demonstrated compatibility failures; contract assemblies need not be empty of useful shared value types.

## Cryptography and secrets

Candidates: `Rfc2898DeriveBytes`, `Pbkdf2`, `SHA1`, `MD5`, AES modes/IVs/nonces, `ProtectedData`, `DataProtectionScope`, logging and format migrations.

- **NET-SEC01 — Weak password derivation for new writes.** Inspect actual PRF, salt randomness/uniqueness, work factor, intended protection, and supported runtime APIs. Assess legacy SHA-1/old PBKDF2 parameters against current authoritative guidance; use a supported derivation API for new designs. Obsolete constructors alone do not prove exploitable cryptography, and read-only legacy compatibility differs from new writes.
- **NET-SEC02 — Encryption lacks authentication or safe nonce/IV handling.** Trace mode-specific uniqueness/unpredictability requirements, tag/MAC verification before plaintext use, key separation, and entropy sources. Prefer an appropriate authenticated construction; fixed/reused AES-GCM nonces are severe. A CBC IV and a GCM nonce have different requirements; do not prescribe one rule for every mode.
- **NET-SEC03 — Broken hashes make trust decisions.** Classify MD5/SHA-1 use for adversarial integrity, authenticity, signatures, or password protection. Use appropriate modern primitives. Non-adversarial cache keys/fingerprints and compatibility readers are not automatically security defects; an unkeyed modern hash alone is not authentication.
- **NET-SEC04 — DPAPI scope mismatches the trust/identity boundary.** Trace protecting and decrypting principals, ACLs, service identity, and machine versus user scope. Machine scope alone is not access control; user scope may fail under another identity. Verify the complete storage/access design before recommending a scope change.
- **NET-SEC05 — Secrets escape through configuration or diagnostics.** Trace plaintext credentials/keys in source, deployed config, DBs, logs, object destructuring, exceptions, or crash artifacts. Check effective protection and access. Immutable strings alone are not a confirmed disclosure; minimize retention where feasible and never reproduce values.
- **NET-SEC06 — Irreversible unversioned crypto migration.** Inspect envelopes for algorithm/parameter versioning, legacy read support, atomic new writes, backup/recovery, and interrupted migration. Avoid destructive migration during ordinary reads that can lose access; assess actual recoverability rather than demanding a particular file format.

## Configuration

Candidates: `ConfigurationManager`, `IConfiguration["..."]`, `App.config`, `Settings.settings`, `appsettings.json`, reload callbacks, writable service config.

- **NET-CFG01 — Settings drift or fail deep in execution.** Trace duplicate defaults/representations across shells and parsing without boundary validation of paths, schedules, ports, limits, or crypto parameters. Use shared validated configuration where warranted. Raw configuration access or absence of options classes alone is not a defect.
- **NET-CFG02 — Reload changes active-operation invariants.** Trace mutable destination/security/scheduler settings read repeatedly during one operation. Snapshot effective settings or coordinate safe reload. Dynamic settings are legitimate when the operation explicitly tolerates changes.
- **NET-CFG03 — Updates overwrite runtime user state.** Inspect mutable settings/data placed in replaced installation content and update migration behavior. Separate deployment assets from user state or preserve/migrate it safely; verify actual updater rules and paths.

## Updates and deployment

Candidates: updater metadata, HTTP URLs, checksums, Authenticode, `RunUpdateAsAdmin`, extraction/install paths, restart logic, shell/service version checks.

- **NET-UPD01 — Downloaded code lacks an authenticated trust chain.** Trace metadata redirects, binaries, expected signer/keys, verification before execution/extraction, and failure handling. A hash delivered beside an unsigned payload is not independent authentication. Establish authenticated releases via signatures or an equivalent trusted distribution mechanism; unsigned metadata is not necessarily exploitable when every allowed artifact is independently authenticated and constrained.
- **NET-UPD02 — Update trust failure crosses an unnecessary elevation boundary.** Inspect elevation requests, writable staging paths, installer launch, and relaunch tokens. Elevate only required installation steps and restore the intended application privilege. Verify library-specific elevation advisories for installed versions; an administrator-required install is not inherently wrong.
- **NET-UPD03 — Installation can leave mixed or unrecoverable versions.** Trace live-file overwrite, interruption, concurrent updaters, locked files, staging, rollback/repair, and protection of user state. Coordinate active operations and updater ownership. Require demonstrated recoverability appropriate to distribution mode, not a custom updater where the platform already supplies it.
- **NET-UPD04 — Shell/service/schema upgrades ignore compatibility.** Trace protocol/schema changes, deployment order, downgrade, and running old processes. Stage compatible changes or coordinate version transitions; independently deployed components are valid with enforced compatibility rules.
- **NET-UPD05 — Dependency support/servicing is assumed.** Inspect updater UI-framework compatibility and security-sensitive libraries against installed versions and actual integration. Verify advisories/support constraints and relevant paths. An older package, package name, or unsupported-but-working integration alone does not establish a high-severity defect; missing evidence belongs in verification notes.

## Preview handlers and COM

Candidates: `IPreviewHandler`, `IInitializeWithStream`, `IInitializeWithFile`, COM activation, `DoPreview`, `Unload`, preview processes, apartment setup.

- **NET-COM01 — Untrusted preview code shares shell privileges/resources.** Trace third-party handlers or document parsers loaded in-process, elevated previews, disabled low-integrity isolation, and access to credentials/services. Prefer an appropriately constrained out-of-process host for untrusted handlers. A separate process without privilege restrictions is not a complete sandbox; in-process rendering of trusted data is a different case.
- **NET-COM02 — Preview initialization grants unnecessary capabilities.** Inspect file-versus-stream initialization and script/macro behavior. Prefer stream initialization where the handler supports it and prevent active content. A handler supporting only file initialization requires risk assessment, not a fictitious stream interface.
- **NET-COM03 — Preview lifetime leaks or conflicts with mutation.** Trace selection changes, `Unload`, stream/COM ownership, file locks, concurrent writer/delete/update access, and expensive work before `DoPreview`. Release according to the handler contract and coordinate access. Avoid indiscriminate forced COM release of shared runtime-callable wrappers.
- **NET-COM04 — Apartment or bitness assumptions break activation/callbacks.** Inspect STA/MTA initialization, cross-apartment marshaling, handler registration, host architecture, and shutdown. Trace an actual mismatch; x64 applications can still need a compatible out-of-process host for other handler architectures.

Microsoft's [preview-handler guidance](https://learn.microsoft.com/en-us/windows/win32/shell/preview-handlers) explains low-integrity hosting and stream initialization; consult the exact interfaces/hosting model in use.

## Native interop

Candidates: `DllImport`, `LibraryImport`, `IntPtr`, `CloseHandle`, `SetLastError`, `GetLastWin32Error`, callbacks, `LoadLibrary`, process launch.

- **NET-NATIVE01 — Incorrect ABI or error handling.** Compare pointer/integer widths, calling conventions, struct layout, bool/string marshaling, Unicode entry points, and last-error capture with native signatures. Show truncation, corruption, wrong paths, or lost errors. Consider source-generated `LibraryImport` where supported; `DllImport` itself is not a defect.
- **NET-NATIVE02 — Native resource/callback ownership is unsafe.** Trace handle closure on success/failure/cancellation, `SafeHandle` alternatives, callback delegate rooting, and native callback lifetime. Manual ownership is valid when correct; flag leaks, double-close, premature collection, or use-after-free evidence.
- **NET-NATIVE03 — Executable/DLL resolution trusts writable search paths.** Trace relative executable names, PATH/working-directory lookup, DLL loading, shell execution, and attacker-writable directories. Resolve trusted targets and constrain search behavior. An absolute path is not sufficient if the target or its parent is attacker-writable.
- **NET-NATIVE04 — Unsafe implementation exceeds its reviewed boundary.** Inspect actual pointer operations and callers for unchecked buffers/lifetimes or avoidable unsafe propagation. Localize interop hazards; project-wide `AllowUnsafeBlocks` permission alone is not a defect.

## Logging and localization

Candidates: Serilog interpolation/destructuring, sink paths/limits/`shared`, catch blocks, correlation properties, `.resx`/`.resw`, culture-sensitive serialization.

- **NET-LOG01 — Logs lose queryable context or actionable errors.** Trace interpolated/dynamic templates, message-only exceptions, inconsistent fields, repeated stack traces, and missing operation correlation across shell/service/jobs. Show the diagnostic or resource impact. Stable templates and full exception context help; source-generated logging is an optimization when measured hot-path cost warrants it.
- **NET-LOG02 — Log ownership/retention creates operational or trust failures.** Inspect file size/retention, disk budget, process sharing, ACLs, installation-directory writes, and service/UI separation. Report evidenced unbounded growth, lock failures, spoofing, or access problems. A shared sink is valid with appropriate coordination and permissions. Secret disclosure belongs under NET-SEC05.
- **NET-LOC01 — Display culture leaks into persisted identifiers/formats.** Trace localized strings used as keys, culture-dependent parsing/serialization, localized log field names, and switching UI culture. Use stable identifiers and explicit interoperable storage formats; keep user-facing formatting culture-appropriate.
- **NET-LOC02 — Resource/fallback/layout behavior breaks supported languages.** Inspect hard-coded UI messages, translated-fragment concatenation, neutral resources/fallback, shell differences, and expanded text/DPI layout. Report against supported localization requirements; an intentionally single-language application is not defective merely for lacking resources.

## Dependencies and build

Candidates: `packages.lock.json`, restore flags, `global.json`, central versions, analyzer configuration and suppressions, native assets.

- **NET-BUILD01 — Claimed reproducibility is not enforced.** Trace CI SDK selection and restore behavior, locked mode, force evaluation, committed lock files, and dependency inputs. Use the installed NuGet version's documented behavior. Lock files alone do not guarantee immutable CI resolution; an intentional library workflow or reproducible alternative may differ.
- **NET-BUILD02 — Security diagnostics are silently ineffective.** Inspect enabled rules, severities, broad crypto/interop suppressions, warning baselines, and actual CI gates. Report rules disabled in relevant hazardous paths or unexplained blanket suppression; installing an analyzer does not enable every rule. Narrow legacy-compatibility suppression can be appropriate.
- **NET-BUILD03 — Dependency changes ignore compatibility or known exposure.** Trace conflicting package/native asset versions and verified advisories in reachable code. Evaluate runtime/provider/RPC/updater compatibility before upgrades; central package management is an option, not a universal requirement. Lock files do not replace servicing, and newest-version preference is not a finding.

## Filesystem operations

Apply to any application that copies, synchronizes, archives, restores, or deletes files; consistency guarantees determine applicability.

- **NET-FS01 — Path validation is defeated by later resolution.** Trace validate-then-reopen, reparse points, normalization/case/device paths, and privileged access. Enforce the intended root/resource at use time using appropriate handle-based checks and policy. Lexical prefix checks alone do not prevent traversal or replacement races.
- **NET-FS02 — Recursive traversal lacks boundaries.** Inspect junction/symlink policies, cycle detection, source=destination, destination-under-source, exclusions, and permissions. Report loops, unintended traversal, or recursive self-copy. Following links intentionally with safe boundaries is valid.
- **NET-FS03 — Required consistency is assumed for live files.** Trace mutations during copy/preview and required application-consistent data. Use suitable snapshots/coordination where necessary and expose consistency level. Immutable files do not inherently need VSS; snapshots alone may not satisfy application consistency.
- **NET-FS04 — Incomplete results are published as successful.** Trace final names/indexes before completion, flush/durability requirements, length/hash/metadata checks, and silent permission skips. Stage, verify, and publish complete state; report partial outcomes explicitly. Match validation/durability to promised guarantees rather than requiring every possible check.
- **NET-FS05 — Retention destroys the last usable state.** Trace deletion ordering, verified replacement/recovery points, external deletion versus metadata updates, crash recovery, and retry. Preserve recoverability appropriate to the operation; deduplicate with NET-SQL05 when the same consistency failure underlies both.
- **NET-FS06 — Path/type assumptions differ across APIs.** Trace extension-based trust, managed/native/COM/remote path normalization, long paths, Unicode, and reserved/device names. Verify supported input handling end-to-end; file extensions alone do not authenticate content or make preview parsing safe.
