---
name: sync-readme-just
description: >-
  Aligns README (and SETUP.md if present) with the project's justfile: removes
  stale commands, documents real recipes, and matches the repo's package runner
  (uv, poetry, npm). Use when updating README, auditing docs, or after migrating
  build tooling.
---

# Sync README with justfile

## Workflow

1. Read `README.md` (and `SETUP.md` if it lists commands).
2. Read `justfile` (and `just --list` if recipes are generated or unclear).
3. Extract every command the README tells users to run (`just …`, `uv …`, `poetry …`, docker, etc.).
4. For each README command:
   - **Exists in justfile** → keep or tighten wording.
   - **Missing** → remove or replace with the actual recipe (e.g. `just setup` instead of a removed helper).
5. Surface useful just recipes that README omits but developers need (`lint`, `sync`, `setup-hooks`) — add only if commonly used.
6. Match the repo's runner naming (`uv run`, `just uv …`) per `.cursor/rules` and CI workflows.
7. Summarize changes as a short bullet list.

## Guardrails

- Do not invent recipes; only document what exists.
- Prefer `just <recipe>` over raw tool commands when the justfile wraps auth/setup (e.g. `just uv sync` vs bare `uv sync` in Celonis repos).
- Do not edit unrelated narrative sections.
