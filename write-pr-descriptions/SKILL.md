---
name: write-pr-descriptions
description: Draft clear, concise pull request descriptions from diffs, issue context, branch changes, or user-provided summaries. Use when Codex needs to prepare or refine a reviewer-facing PR description that explains the problem or feature, user or system impact, important context, repository-specific template requirements, and focused review callouts without narrating implementation details.
---

# Write PR Descriptions

Create a polished, ready-to-paste description that helps busy reviewers understand what the PR delivers and why it matters.

## Workflow

1. Gather the available context: inspect the diff, PR title, issue or task, relevant commit messages, and any user-provided notes. Use only supported facts; flag missing context instead of guessing.
2. Identify the delivered outcome, the problem or feature it addresses, and the user-facing or system-level impact. Use the project's glossary vocabulary when available, following `GLOSSARY-MAP.md` or legacy `CONTEXT-MAP.md` to the relevant `GLOSSARY.md` / `CONTEXT.md`.
3. Look for the repository's GitHub PR template when a repository is available, including `.github/pull_request_template.md` and templates under `.github/PULL_REQUEST_TEMPLATE/`. If a template exists, preserve its required headings, checkboxes, prompts, and formatting; when multiple templates exist, use the one that best matches the change. If no template is available, use the default concise format.
4. Check for context reviewers may need: related issues, dependencies, migrations, rollout constraints, breaking changes, or compatibility concerns.
5. Add a review callout only when a specific area genuinely deserves careful attention, such as complex behavior, edge cases, security, data integrity, or an architectural decision.
6. Write the shortest clear description, usually 2–4 sentences plus relevant verification. Use bullets for distinct deliverables or risks. Larger changes may use `## Why`, `## What changed`, `## Scope`, `## Tradeoffs`, `## Blast Radius`, and `## Verification`; omit sections that add no useful information. A repository template takes precedence.

## Writing Rules

- Describe what the PR delivers, not how it is implemented.
- Lead with the problem solved or capability added, then state the resulting impact.
- Keep the language high-level and concrete; assume reviewers can inspect the diff for technical specifics.
- Mention file names, functions, or implementation details only when they identify a meaningful review risk or decision.
- Include dependencies, breaking changes, and related context when they affect review or adoption.
- Name actual validation commands or run paths and their observed outcomes. Do not imply unrun checks passed. For performance, use one primary before/after number with units and link the run count, variation, and limiting resource.
- For a visual or behavioral change, include available before/after evidence: screenshots, exact output, or the failing/passing regression check. Link artifacts and identify what they demonstrate; name missing evidence without inventing a baseline.
- Use a small diagram, pseudocode, call tree, or diff sketch when it makes the change easier to review than prose. Keep only the relationships needed to explain the result; a simple PR may need no visual.
- For changes with material merge risk, state whether rollback is straightforward or requires recovery or migration, and identify the affected callers, data, or user flows. Explain irreversible effects and concrete mitigations rather than adding a generic risk label to every PR.
- State exclusions only when they clarify a meaningful boundary or follow-up.
- When publishing or editing a PR in T3, use the built-in PR mutation tool if provided and register the full URL with `link_pull_request`. Check `list_thread_pull_requests` before finishing PR work. Drafting text alone does not authorize publishing it.
- Omit line-by-line summaries, obvious refactors, generic file lists, design-pattern explanations, and unsupported claims.
- Return the description directly unless the user asks for analysis, alternatives, or a separate review checklist.
