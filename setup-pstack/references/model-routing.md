# Model configuration and delegation

Read this before delegating in a pstack workflow. Runtime instructions take precedence over Cursor-specific `Task` fields, model defaults, and scheduling examples in older playbooks.

## Resolve a role

In T3, call `orchestrator_capabilities` to discover provider instances, model IDs, and reasoning options from the live composer catalog. Read `~/.agents/pstack-models.json` if it exists. The `roles` object uses pstack's existing role labels. A missing role, `auto`, or `inherit-parent` inherits the current provider and model. A panel role is a list; every entry is a seat, including inherited entries. An unconfigured panel uses inherited seats sufficient for its declared coverage, with distinct design briefs when exploring alternatives.

A concrete entry has `providerInstanceId`, `model`, and optional `options`, using IDs and values from the catalog. Reasoning is a model option, not a suffix to invent. Validate every entry before launching. If an entry is unavailable, report it and use inherited execution for that seat rather than guessing a slug. Prefer a different available provider or model family for an independent cross-judge when configured; never claim model diversity when all seats inherit the same model.

In Cursor, read `~/.cursor/rules/pstack-models.mdc` and use its role lines and confirmed `Task` model slugs. In other hosts, use the native delegation catalog and inherited execution when no configuration is available. A preference file is read by this workflow; it is not an automatically applied host rule.

## Launch child work

Prefer native subagent tools for same-provider work only when they support the chosen model. Use T3 `delegate_task` for cross-provider models, unsupported native model IDs, or explicitly T3-owned child tasks. Pass the selected provider/model/options through `target`, use `mode: "async"`, and retain the returned `taskId`. Manage it with `task_status` or `task_cancel`. Use a stable `clientRequestId` for retries of one launch and a different ID for each new round.

Give every task a standalone brief with scope, file pointers, verification, and output paths. Fresh correction rounds include the original brief, later directives, prior findings, responses, unresolved objections, and the prior branch and head SHA. Read-only work is a scope constraint in the brief; do not assume Cursor's `readonly` flag exists or that native tool fields apply to T3.

`delegate_task` inherits the caller's project, branch, and worktree. Asking it to `cd` or create a worktree does not change that binding. Use disjoint writable outputs in the shared checkout. When isolated binding is required, use an available native isolated execution facility or serialize the writes. Creating a top-level T3 thread requires the user's explicit request for separate conversations.

For each T3 review round, call `delegate_task` again. Do not resume the backing `childThreadId` through thread messaging. Existing live checkout or process state can justify native agent reuse where supported, but cannot override T3's fresh delegated-review requirement.

Respect concurrency limits and record missing results as gaps. Completion notifications wake the parent; avoid polling loops. Use a bounded status read when the result is needed during an active turn.
