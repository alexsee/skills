---
name: tdd
description: Develop a feature or bug fix test-first in red-green-refactor cycles. Use when the user requests TDD, red-green-refactor, or test-first work.
---

# Test-Driven Development

Work in vertical slices: one failing behavioral test, the smallest implementation
that passes, then a refactor when it improves the design. Repeat until the
requested behavior is complete.

Test through a public interface already used by callers or established tests.
Choose the narrowest seam that demonstrates the behavior; ask only when seam
placement requires an unresolved product or architectural decision.

Keep tests independent of implementation structure. Avoid private-method tests,
mocks of internal collaborators, tautological expected values, and bulk tests
for imagined future behavior. Expected results should come from a spec, worked
example, known-good literal, or another independent source of truth.

Read [`tests.md`](tests.md) for detailed examples or [`mocking.md`](mocking.md)
when test doubles are central to the current slice.
