# pluck

```text
Operator:       pluck
Stream shape:   T* → (R | undefined)*
State:          Unit
Initial state:  ()
Value step:     (Unit × T) → (Unit × (R | undefined)) — read the property path
Ownership:      No remembered source data; one source connection per subscription
Lifecycle:      Forward completion; source/access errors terminate; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `pluck(...keys)`; fixed, nonempty property path · **Format:** Compact

> Each source value is replaced by the value found at the configured property path.

## Notation and scope

- `R` is the value type at the final property. If traversal encounters a nullish intermediate or an undefined property, emit `undefined`; this is a value, not a dropped emission.
- `Unit` is semantic value memory: the operator retains the fixed path, but remembers no earlier input. Property access may invoke user-defined getters, whose thrown errors terminate the result.
- An empty path throws when constructing the operator. This deprecated form is available from `rxjs/operators`; an explicit [map](map.md) projection is its replacement.
- Each input produces one output while open. There is no added scheduler or sharing, and values reached through the path are not cloned.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/pluck.ts)
- [Delegated map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/map.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
