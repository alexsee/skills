---
name: refactor-pass
description: Refactor the changes from the current task for simplicity while preserving behavior. Use when the user explicitly asks for a final cleanup pass.
---

# Refactor Pass

## Workflow

1. Review the changes just made and identify simplification opportunities.
2. Apply refactors to:
   - Remove dead code and dead paths.
   - Straighten logic flows.
   - Remove excessive parameters.
   - Remove premature optimization.
   - Remove extra comments that are unnecessary or inconsistent with local style.
   - Remove casts to `any` used only to bypass type issues.
   - Refactor deeply nested code that should be simplified with early returns.
3. Run the narrowest existing build or tests that cover the refactor.
4. Identify optional abstractions or reusable patterns; only suggest them if they clearly improve clarity and keep suggestions brief.

## Guardrails

- Keep behavior unchanged unless fixing a clear bug.
- Prefer minimal, focused edits over broad rewrites.
- Do not add tests that merely mirror a mechanical refactor.
