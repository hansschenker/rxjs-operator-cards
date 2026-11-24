# flatMap

```text
Operator:       flatMap
Stream shape:   T* → R*
State:          InnerId[] × Nat × Bool
Initial state:  ([], 0, false)
Value step:     (S × T) → (S × Action[]) — project with index and connect another inner
Ownership:      One active-inner set and index per operator subscription
Lifecycle:      Outer complete waits for all inners; errors close all; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `flatMap(project)`; unlimited concurrency, no result selector · **Format:** Compact

> Each source value starts an inner stream whose values may overlap those from other active inners.

## Notation and scope

- `flatMap` is the deprecated name bound directly to [mergeMap](mergeMap.md), available from `rxjs/operators` and `rxjs`; it has the same implementation and overloads.
- `S` contains active inner identities, the next zero-based projection index, and the outer-completed flag. Every inner next forwards its payload; an inner completion releases that connection.
- This scope models Observable-valued projections and the default unlimited concurrency. A finite bound adds an outer-value queue; the deprecated result selector is also omitted.
- No scheduler or sharing is introduced. Projection errors and errors from any active input terminate the result and close all owned connections.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/flatMap.ts)
- [Merge-map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeMap.ts)
- [Merge coordination](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
