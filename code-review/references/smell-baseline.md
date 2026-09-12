# Maintainability smell baseline

Treat these as judgment calls, never hard violations. A repository rule wins
when it intentionally favors a different design.

- Mysterious names that hide purpose.
- Duplicated logic likely to drift.
- Feature envy or message chains that expose another module's internals.
- Repeated parameter groups that represent one concept.
- Primitives standing in for meaningful domain types.
- Repeated switches over the same variants.
- One change scattered across many unrelated locations.
- One module changing for several unrelated reasons.
- Speculative abstractions, options, or extension points.
- Pass-through wrappers and middle-man modules with no useful policy.
- Inheritance whose contract is mostly ignored.
- Unnecessary nesting, parameters, comments, casts, or optimization.

Apply the deletion test before recommending extraction: if removing an
abstraction makes complexity disappear, it may be unnecessary; if complexity
spreads into callers, the abstraction is likely earning its place.
