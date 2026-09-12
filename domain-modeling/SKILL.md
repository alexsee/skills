---
name: domain-modeling
description: Define or revise domain vocabulary in CONTEXT.md, or record a durable architectural decision in an ADR.
---

# Domain Modeling

Sharpen the project's language while discussing or changing the model. Check
existing `CONTEXT.md`, `CONTEXT-MAP.md`, and relevant ADRs only when they exist
and apply to the current area.

Resolve vague or conflicting terms with concrete scenarios and code evidence.
Ask the user only when different meanings would materially change the model.
When terminology is settled and the task authorizes documentation changes,
update the relevant `CONTEXT.md` using
[`CONTEXT-FORMAT.md`](CONTEXT-FORMAT.md). Keep it a glossary of domain concepts,
relationships, and invariants; omit implementation details and feature plans.

Record an ADR only for a decision that is costly to reverse, surprising without
context, and based on a real tradeoff. Use [`ADR-FORMAT.md`](ADR-FORMAT.md).
Create glossary or ADR files lazily when there is a durable decision to capture.
