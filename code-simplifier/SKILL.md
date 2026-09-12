---
name: code-simplifier
description: Simplify recently changed code without changing behavior. Use when the user asks for cleanup, simplification, or a maintainability refactor.
---

# Code Simplifier

Work on the requested scope, defaulting to code changed in the current task.
Follow the repository's actual conventions rather than assuming a language,
framework, or `CLAUDE.md` policy.

Remove accidental complexity: dead paths, redundant wrappers, premature
abstractions, needless parameters, duplicated logic, excessive nesting, and
comments that merely restate code. Prefer readable control flow and names over
fewer lines.

Preserve observable behavior. If a simplification exposes a separate bug, report
it rather than silently expanding the task. Validate with the narrowest existing
checks that cover the changed behavior; do not add tests that only mirror the
refactor.
