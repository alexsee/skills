---
name: resolving-merge-conflicts
description: "Use when you need to resolve an in-progress git merge/rebase conflict."
---

1. **See the current state** of the merge/rebase. Check git history, and the conflicting files.

2. **Find the primary sources** for each conflict. Understand deeply why each change was made, and what the original intent was. Read the commit messages, check the PRs, check original issues/tickets.

3. **Resolve each hunk.** Preserve both intents where possible. Where incompatible, pick the one matching the merge's stated goal and note the trade-off. Do **not** invent new behaviour. Continue resolving within the authorized scope. If the intents cannot be reconciled from available evidence, explain the conflicting behavior and ask a focused question. Do not abort unless the user requests it.

4. Run the project's relevant build and focused checks for the resolved changes. Broaden verification only for integration risk or failures, and fix regressions caused by the merge.

5. **Finish the merge/rebase.** Stage only resolved files belonging to this merge/rebase, preserving unrelated work. When completion is authorized, commit the merge or continue the rebase until complete. Honor an explicit request to leave the result uncommitted.
