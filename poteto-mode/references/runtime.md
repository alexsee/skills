# Host runtime integration

Read this when a playbook delegates, schedules audits, controls an app, or manages PRs. The host's tool instructions override platform-specific examples in the playbooks.

## Delegation and models

Use [model-routing.md](../../setup-pstack/references/model-routing.md). In T3, choose models from `orchestrator_capabilities` and retain delegated task IDs. Each fresh review round carries the complete brief and prior findings. Cursor-specific agent types, cloud VM fields, and model slugs apply only in Cursor. Inherited execution is the portable default.

## Scheduled audit ticks

An explicit go for an autopilot program that includes recurring audits authorizes its audit schedule. In T3, use `schedule_task` with the structured schedule `{type: "interval", everyMs: 3600000}`, `bindToCurrentThread: true`, a stable request ID, and the program's audit prompt. Retain `scheduledTaskId` and report the returned schedule and `nextRunAt`. Discover the scheduler's update/cancel tools to disable that task when the program finishes or the user stops it. Do not create a new schedule on every wake. Recurrence is not authorization for new scope or merging.

In Cursor, use `/loop 1h` when supported. In hosts without persistent scheduling, report that limitation; do not emulate persistence with a sleeping shell or claim a tick was armed. Create a goal only when the user explicitly requests one.

Read playbooks from the actual skills installation. Use `git show origin/main:<resolved path>` only when that repository owns the installed source. Do not assume every project has a `pstack/skills` tree or fetch another project's trunk for instructions.

## PR creation, registration, and monitoring

Use the host's built-in PR mutation tool when provided, otherwise the resolved forge CLI. In T3, call `link_pull_request` immediately after creating or starting work on each PR, including every stack layer. Before finishing, check `list_thread_pull_requests` and link any missing PR from this work. Report linking failures.

For a monitoring or babysitting request in T3, handle existing findings, call `watch_pull_request`, and end the turn. Resume on the app's wake. Do not run a separate watcher, `/loop`, sleep, or polling process. A one-time status request needs only a status read. On a wake, inspect the current head, checks, comments, and merge state; a notification does not authorize merging. Outside T3, use the playbook's forge-specific watcher.

Replying to review comments is an external message. Do so only when explicitly authorized by the user's request or an invoked workflow that authorizes it; PR inspection alone does not authorize posting.

## Control surfaces

For browser work in T3, call `preview_status`, then `preview_open` when no automation-capable preview is attached. Prefer its snapshots and focused interaction tools. For mobile, use `device_list`, `device_open`, and the returned launcher and session flags. Use available native control tools or CLI commands for other surfaces. Do not require unavailable Cursor plugins or switch browser systems merely because the preview starts closed.
