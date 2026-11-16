### Opening a PR

Apply [host runtime integration](../references/runtime.md) before delegating, scheduling, controlling an app, or managing PRs.

Invoked at the end of every other playbook.

**Worktree.** Use an isolated checkout when the host supports correctly bound child work. T3 delegated tasks share the caller's checkout by default; pass `isolation: "worktree"` for a correctly bound separate checkout starting at the parent local commit, without uncommitted edits. Use disjoint outputs or serialize overlapping writes. Preserve unrelated dirty work. Do not reset or discard it to prepare a PR. Keep commits small and landable. Inspect the diff before commit, using `deslop` when installed.

Title: `type(scope): subject`, naming a real symbol when one carries the change. Body: a one-minute briefing under 40 lines with these `##` headings in order, dropping empty ones: `## Why`, `## What changed`, `## Scope`, `## Tradeoffs` (only rejected alternatives a reviewer would otherwise ask about), `## Blast Radius`, `## Verification`.

Stack follow-ups by base branch. Open PRs ready, through the run's PR tool with `draft: false`. Without one, use `gh pr create` without `--draft`, or `origin pr create --status open` when Origin's CLI resolves the repository. Link every created or pushed PR immediately with `link_pull_request`, including each stack layer. Children return the PR URL, head SHA, checks, and reviews to the parent and never watch. Opening a PR does not start a babysit.
