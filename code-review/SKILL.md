---
name: code-review
description: Review a branch, PR, or worktree against repository standards and its originating requirements, using independent review passes.
---

# Code Review

Review the requested diff along two separate axes:

- **Standards:** repository instructions and relevant maintainability concerns.
- **Spec:** the issue, spec, or accepted conversation requirements.

## Scope

Honor an explicit comparison point. For a PR, use its base. For committed branch
work, infer the merge base from repository or PR context. For uncommitted work,
review staged and unstaged changes plus relevant untracked files. State the
resolved scope and validate that it contains changes.

Find the spec from linked issues, user-provided references, repository specs, or
the conversation. If none exists, report that the Spec axis is unavailable and
continue the Standards review without pausing.

Use standards that apply to the changed area. Include
[`references/smell-baseline.md`](references/smell-baseline.md) as a heuristic,
with repository rules taking precedence.

## Independent passes

Run Standards and Spec reviews independently and in parallel when delegation is
available. Give each reviewer the resolved diff scope and only the context for
its axis. If there is no spec, skip that reviewer. If delegation is unavailable,
perform the same passes sequentially.

Report only concrete, actionable findings that the author would likely fix.
Include severity, file, and line or hunk. Distinguish documented violations from
maintainability judgments and omit style issues enforced by tooling.

Present `## Standards` and `## Spec` separately, then give finding counts for
each. Do not merge or rerank the axes.
