---
name: implement
description: Implement an existing issue, spec, or ticket end to end. Use when the user explicitly invokes this workflow with a work item.
---

# Implement

Read the referenced work item and relevant repository guidance, infer routine
details from the codebase, and carry the authorized scope to a working result.
Treat accepted conversation decisions as part of the requirements.

Use TDD only when requested or when a focused regression test materially
improves confidence. Run the narrowest meaningful build and tests for the
change, broadening only for concrete integration risk or failures.

Finish implementation, verification, and any directly required generated
artifacts. Do not add a separate review workflow or commit unless the user asks.
Report remaining limitations plainly.
