# mergeMapTo

```text
Operator:       mergeMapTo
Stream shape:   T* → R*
State:          InnerId[] × Bool
Initial state:  ([], false)
Value step:     (S × T) → (S × Action[]) — create another subscription to the fixed inner
Ownership:      One active-inner set per operator subscription
Lifecycle:      Outer complete waits for all runs; any error closes all; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `mergeMapTo(inner)`; fixed Observable `inner: R*`, unlimited concurrency, no result selector · **Format:** Compact

> Each source value starts another subscription to the same inner description, allowing their values to overlap.

## Notation and scope

- `S` contains active inner identities and the outer-completed flag. Source payloads and projection indexes are unused in this constant-projection scope.
- Each inner next forwards its value; each inner completion removes that run. The result completes only when the outer has completed and every accepted run has ended.
- This deprecated operator delegates to [mergeMap](mergeMap.md), and is available from `rxjs/operators`. A finite concurrency bound adds a trigger queue; the result-selector overload is omitted.
- The same Observable description can have multiple subscriptions. Cold inners execute separately; explicitly shared inners keep their sharing policy. No scheduler is added.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeMapTo.ts)
- [Delegated merge-map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
