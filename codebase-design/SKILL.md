---
name: codebase-design
description: Design or improve module interfaces and seams using deep-module principles. Use for architecture, interface placement, or testability decisions.
---

# Codebase Design

Use deep modules as a design lens: callers should learn a small interface that
hides meaningful behavior and concentrates change.

## Vocabulary

- **Module:** anything with an interface and implementation, at any scale.
- **Interface:** everything a caller must know, including invariants, errors,
  ordering, configuration, and performance constraints.
- **Seam:** a place where behavior can vary without editing the caller.
- **Adapter:** an implementation selected at a seam.
- **Depth:** capability hidden behind a comparatively small interface.
- **Leverage:** value delivered to many callers through that interface.
- **Locality:** related knowledge, changes, bugs, and verification staying
  together.

Prefer fewer, simpler interface concepts. Accept dependencies at genuine seams,
and keep optional internal structure out of the caller-facing contract. A seam
is justified by real variation, policy, isolation, or testing value—not by a
hypothetical future implementation.

Use the deletion test: if removing a module makes complexity disappear, it was
probably a wrapper; if the complexity spreads across callers, the module was
providing depth.

For a detailed deepening analysis, read [DEEPENING.md](DEEPENING.md). When the
interface shape is uncertain and alternatives would help, read
[DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md).
