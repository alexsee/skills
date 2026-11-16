# Host runtime integration

Read this when a playbook delegates, schedules audits, controls an app, or manages PRs. The host's tool instructions override platform-specific examples in the playbooks.

## Delegation and models

Use [model-routing.md](model-routing.md). In T3, choose models from `orchestrator_capabilities` and retain delegated task IDs. Each fresh review round carries the complete brief and prior findings. Cursor-specific agent types, cloud VM fields, and model slugs apply only in Cursor. Inherited execution is the portable default.

## Scheduled audit ticks

An explicit go for an autopilot program that includes recurring audits authorizes its audit schedule. In T3, use `schedule_task` with the structured schedule `{type: "interval", everyMs: 3600000}`, `bindToCurrentThread: true`, a stable request ID, and the program's audit prompt. Retain `scheduledTaskId` and report the returned schedule and `nextRunAt`. Discover the scheduler's update/cancel tools to disable that task when the program finishes or the user stops it. Do not create a new schedule on every wake. Recurrence is not authorization for new scope or merging.

In Cursor, use `/loop 1h` when supported. In hosts without persistent scheduling, report that limitation; do not emulate persistence with a sleeping shell or claim a tick was armed. Create a goal only when the user explicitly requests one.

Read playbooks from the actual skills installation. Use `git show origin/main:<resolved path>` only when that repository owns the installed source. Do not assume every project has a `pstack/skills` tree or fetch another project's trunk for instructions.

## PR creation, registration, and monitoring

Use the host's built-in PR mutation tool when provided, otherwise the resolved forge CLI. In T3, call `link_pull_request` as the next call after creating, taking over, or pushing to each PR, including every stack layer. Before finishing, check `list_thread_pull_requests` and link any missing PR from this work. Report linking failures.

For a monitoring or babysitting request in T3, or when requested merge work still waits on checks or reviews, finish branch updates and pushes, handle existing findings, call `watch_pull_request`, and end the turn. Children never watch; they return the PR URL, head SHA, and checks/reviews state to the parent. Resume on the app's wake. Do not run a separate watcher, `/loop`, sleep, or polling process. A one-time status request needs only a status read. On a wake, inspect the current head, checks, comments, and merge state; a notification does not authorize merging. When explicitly handing monitoring back, call `unwatch_pull_request`. Outside T3, use the playbook's forge-specific watcher.

Replying to review comments is an external message. Do so only when explicitly authorized by the user's request or an invoked workflow that authorizes it; PR inspection alone does not authorize posting.

## Control surfaces

For browser work in T3, call `preview_status`, then `preview_open` when no automation-capable preview is attached. Prefer its snapshots and focused interaction tools. For mobile, use `device_list`, then `device_open` with host and device IDs. Use its returned `agent-device` command for launches and interaction, and `device_screenshot` to inspect the screen; do not drive the device with simctl, adb, or ssh. Build and install commands may use their normal tooling. Use available native control tools or CLI commands for other surfaces. Do not require unavailable Cursor plugins or switch browser systems merely because the preview starts closed.

## Local skills, history, and secrets

Use this checkout at `~/.agents/skills` for personal skills and `.agents/skills/<name>/` for new project-local skills, preserving existing target locations when requested. Use the installed **skill-creator** skill; inspect the diff directly when optional cleanup plugins are absent. Specialized agents are available only when their policy is installed; report a missing specialist rather than pretending its pass ran.

Read in-scope history through T3 thread search/read tools or explicit host-provided transcript paths. Read a named thread before reporting on it and cite its returned link verbatim. Do not assume a Cursor transcript directory. For required passwords, tokens, or keys the user must supply, use `request_secret`, never chat or a Cursor secret card.

Host authorization and tool instructions govern every workflow. A skill does not grant external-message, scheduling, publishing, or merge permission beyond the user's task. Discover tool capabilities before use and disclose missing integrations without claiming an equivalent exists.
