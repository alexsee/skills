---
name: verify-and-fix
description: >-
  Confirms a reported bug exists, fixes the root cause with a minimal diff, and
  runs focused tests. Use when the user says "Verify this issue exists and fix
  it", pastes a Bugbot/review finding, or describes a concrete defect to
  validate and repair.
---

# Verify and fix

## Workflow

1. **Restate the claim** in one sentence (expected vs actual).
2. **Verify** by reading the cited file/lines. Say clearly: confirmed, not reproduced, or partially true.
3. **Fix the root cause**, not symptoms:
   - Prefer correct control flow (`else` / early return) over catching errors the code should avoid.
   - Do not broaden `except` to hide logic bugs.
   - Keep the diff minimal and scoped to the reported issue.
4. **Check for the same mistake nearby** (copy-paste, sibling branches).
5. **Validate** with the narrowest check that proves the fix (unit test for that path, or `read_lints` on touched files).
6. **Report** briefly: what was wrong, what changed, how you verified.

## Guardrails

- If the report is wrong, explain why and stop — do not "fix" working code.
- Do not refactor unrelated code in the same pass.
- Do not commit unless the user asks.
