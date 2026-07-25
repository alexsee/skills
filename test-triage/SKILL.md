---
name: test-triage
description: >-
  Triages failing or flaky tests: groups failures, finds root cause, applies
  minimal fixes, and re-runs the smallest relevant test set. Use when pytest/unit
  tests fail after a change, imports break, fixtures drift, or the user asks to
  fix test errors.
---

# Test triage

## Workflow

1. **Run the smallest failing scope** the user named; otherwise one failing file or the last CI test job output.
2. **Group failures** by root cause (import/path move, missing env, assertion drift, fixture, flaky timing).
3. **Fix one root cause at a time** — prefer updating tests when behavior intentionally changed; fix production code for real bugs.
4. **Respect project runners**: use `just test`, `just uv run pytest …`, or rules in `.cursor/rules` — not a different tool than the repo uses.
5. **Re-run** only affected tests until green; then offer full suite if scope was narrow.
6. **Summarize**: failure cause, files changed, commands run.

## Common patterns

| Signal | Likely fix |
|--------|------------|
| `ModuleNotFoundError` after move | Update imports and test package layout |
| Missing model/transport fields | Align test factories with schema changes |
| Guardrail/library swap | Replace tests for removed module with new package tests |
| Env/auth errors locally | Use documented `just` setup; do not weaken CI guards |

## Guardrails

- Do not delete tests to make CI green unless the behavior was removed on purpose.
- Do not skip/xfail without stating why.
- Do not commit unless asked.
