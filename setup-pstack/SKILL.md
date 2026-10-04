---
name: setup-pstack
description: Configure pstack models per role and reasoning budget using the current host's live model catalog. Use for /setup-pstack, "configure pstack models", "pstack budget", or changing pstack model choices.
---

# Setup pstack

Configure role preferences for the current host. Read [references/model-routing.md](references/model-routing.md) for discovery and execution semantics. Do not change live model settings or launch agents as part of setup.

## Discover and load

In T3, use `orchestrator_capabilities` and load `~/.agents/pstack-models.json` if present. In Cursor, enumerate confirmed `Task` slugs and load `~/.cursor/rules/pstack-models.mdc`. In another host, use its available catalog and a user-selected preference path. Never write an unverified provider, model, or option value.

Preserve current role choices. On first setup, start with inherited execution rather than hardcoded model families. If migrating a Cursor rule to T3, retain its intent only where the catalog confirms an equivalent; flag unresolved entries instead of translating slugs by guesswork. Drop retired roles and show what was dropped.

## Choose a budget and roles

Ask for the budget and model preferences together when possible. Offer unlimited, large, medium, and small. Unlimited retains each selected model's effort; the other budgets target xhigh, high, and medium respectively. Apply those only when the live catalog exposes them, choosing the highest supported effort at or below the target. If no effort is exposed, retain the model's setting and disclose that limitation. Preserve `auto` and `inherit-parent`.

Show a concrete role table for review before persisting it. Use these role labels:

- `feature, refactoring`, `bug-fix`, `perf-issue`, `hillclimb`
- `judgment and prose`, `hardest tasks`
- `how explorer`, `how explainer`
- `why investigators`, `why synthesizer`
- `reflect tooling`, `reflect judgment, divergent, synthesizer`
- `arena runners`, `arena cross-judge pool`, `swarm workers`
- `architect runners`, `interrogate reviewers`

`arena runners`, `architect runners`, and `interrogate reviewers` are lists with one agent per entry. `arena cross-judge pool` is a list from which one judge is selected. Other roles are single entries. List length controls seat count, not concurrency. `auto` and `inherit-parent` both inherit the parent provider/model.

If the user already supplied concrete choices and authorized writing them, proceed after validation. Otherwise ask whether to accept the table or change specific roles.

## Validate and persist

Recheck every concrete choice against the live catalog. Write only the selected host's configuration, preserving unrelated files.

For T3, atomically replace `~/.agents/pstack-models.json`. This is an explicit preference file read by the pstack skills, not an always-applied T3 rule. Shape:

```json
{
  "version": 1,
  "budget": "medium",
  "roles": {
    "feature, refactoring": "inherit-parent",
    "arena runners": ["inherit-parent", "inherit-parent"],
    "arena cross-judge pool": ["inherit-parent"]
  }
}
```

Replace aliases with objects containing confirmed `providerInstanceId`, `model`, and optional `options` when concrete models are selected. Copy reasoning option IDs and values from the catalog. Missing roles inherit execution.

For Cursor, write the existing `.mdc` format with `alwaysApply: true`, a budget comment, and one line per role. Adjust reasoning suffixes only to slugs confirmed available; preserve aliases.

Report the path, effective role choices, inherited fallbacks, and any budget limitations. The workflows read these preferences on their next invocation.

## Optional verification skill

If the project lacks a way to drive the actual app and the user wants one, use the available `create-verification-skill`. Model setup alone does not authorize creating a project harness.
