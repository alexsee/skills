# WinForms anti-pattern catalog

Read for Windows Forms applications alongside [shared .NET desktop patterns](dotnet-desktop.md). Stable `FORMS` IDs cover shell-specific behavior. Shared business logic, thread blocking, DI, secrets, and lifetime problems belong in the shared catalog; avoid duplicate findings for one underlying cause.

Candidates: form event handlers, concrete control access, static `Program` state, `Application.DoEvents`, `Control.Invoke`, `InvokeAsync`, `BackgroundWorker`, `FormClosing`, `NotifyIcon`, DPI configuration, `.Designer.cs`, `.resx`.

## Responsiveness and thread dispatch

- **FORMS01 — `Application.DoEvents()` permits unsafe reentrancy.** Trace message pumping during an incomplete operation and handlers that can mutate/dispose its state or start it twice. Prefer awaited/off-thread work and explicit operation state. A match alone is not enough; show the reentrant path and violated invariant.
- **FORMS02 — Synchronous invocation participates in a wait cycle.** Trace `Control.Invoke` while the UI waits on the caller, holds a required lock, or is closing. Prefer awaitable dispatch (`InvokeAsync` on supported modern .NET) or a correctly owned asynchronous posting strategy. Synchronous invocation without a demonstrated blocking cycle is not automatically wrong; do not suggest APIs unavailable to the target framework.
- **FORMS03 — Worker code touches controls unsafely.** Trace callbacks accessing controls/handles outside their owning UI thread, including premature handle creation, misleading `InvokeRequired` checks before handle creation, and dispatch after disposal. Check readiness and marshal through the real owner. Disabling `CheckForIllegalCrossThreadCalls` hides diagnostics rather than fixing access; combine with NET-ASYNC03/06 where appropriate.
- **FORMS04 — BackgroundWorker/Task bridges lose completion or cancellation.** Inspect overlapping worker runs, ignored `RunWorkerCompleted` errors, cancellation that is never checked, and mixed worker/Task ownership. Report the actual error/lifetime problem; BackgroundWorker is not inherently defective and does not need replacement solely for age.

Microsoft's [WinForms thread-safety guidance](https://learn.microsoft.com/en-us/dotnet/desktop/winforms/controls/how-to-make-thread-safe-calls) describes control affinity and the target-framework constraints for `InvokeAsync`.

## Forms, tray, and shutdown lifetime

- **FORMS05 — Form/controller ownership leaks UI state.** Trace long-lived controllers/static `Program` fields retaining disposed forms, manipulating concrete controls across instances, or combining form lifetime with domain operations. Report stale controls, leaks, or divergent business behavior; small presentation adapters are normal. Shared architecture issues use NET-ARCH01/03/04.
- **FORMS06 — `FormClosing` blocks or exits before required cleanup.** Inspect long synchronous work, async handlers returning before completion, repeated close requests, cancellation, and active updates/transfers. Coordinate a bounded close workflow and required persistence; event handlers do not make the event source await their asynchronous continuation. An intentional prompt/cancel-close flow is valid.
- **FORMS07 — Tray lifetime is implicit or races disposal.** Trace `NotifyIcon`, `ApplicationContext`, main-form close/minimize behavior, timer/menu callbacks, exit commands, and icon disposal. Show premature scheduler termination, orphaned process/work, or access to disposed controls. Tray-only applications are legitimate with an explicit owner and exit path.

## DPI, designer, and resources

- **FORMS08 — DPI assumptions break supported monitors.** Inspect `HighDpiMode`, manifests, `AutoScaleMode`, custom drawing, cached sizes/bitmaps, and DPI-change handling. `SystemAware` may be intentional but does not provide per-monitor adaptation. Report demonstrated or directly traced clipping/incorrect sizing under supported DPI changes; compare multiple shells only where parity is required.
- **FORMS09 — Fixed layout fails resizing/localization/accessibility.** Trace absolute pixel assumptions, measured text, anchoring/docking/layout containers, and supported fonts/languages. Show overlapping/clipped content or inaccessible actions. Fixed dimensions are not inherently wrong for a deliberately fixed dialog that scales correctly.
- **FORMS10 — Regeneration or deployment destroys runtime/custom state.** Inspect manual behavior edits inside designer-generated regions and user data stored in compiled `.resx` resources. Show likely regeneration loss or state that cannot persist/is overwritten. Designer files contain legitimate generated layout, and carefully managed serialization/custom designer support is different from arbitrary edits; `.resx` localization resources are appropriate.
