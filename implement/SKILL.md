---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Use TDD when requested or when a behavioral regression benefits from it. Reuse existing test interfaces and any seams already agreed in the task; choose routine test placement without another approval step.

Run the relevant build/typecheck and focused tests according to the repository guidance. Use the full suite only for broad integration risk or an explicit requirement. After appropriate checks pass, repeat or broaden them only for new changes, failures, or unresolved concerns.

Once done, use /code-review to review the work.

Do not commit unless the user has requested it. Complete the authorized implementation without stopping for routine design approvals.
