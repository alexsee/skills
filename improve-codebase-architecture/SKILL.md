---
name: improve-codebase-architecture
description: Find high-value opportunities to deepen modules, present them visually, and explore a selected candidate with the user.
---

# Improve Codebase Architecture

Use the `codebase-design` principles and the project's domain vocabulary. Honor
relevant ADRs instead of reopening settled decisions without evidence.

## Find candidates

Use the scope named by the user. Otherwise start with recently changing areas
and widen only when no meaningful pattern emerges. Look for shallow wrappers,
scattered policy, leaky seams, duplicated orchestration, and behavior that is
hard to verify through a stable interface. Apply the deletion test and exclude
speculative refactors with no current payoff.

## Present the review

Create a self-contained HTML report in the OS temporary directory using
[`HTML-REPORT.md`](HTML-REPORT.md). For each candidate show the relevant files,
current friction, proposed direction, locality and leverage benefits, a compact
before/after visual, and confidence (`Strong`, `Worth exploring`, or
`Speculative`). End with the best first candidate. Open the report when the
environment supports it and provide its path.

Do not design detailed interfaces until the user selects a candidate. Then use
focused grilling to settle constraints and interface choices. Update domain
documentation only when the selected design establishes durable vocabulary or a
decision that meets the project's ADR threshold.
