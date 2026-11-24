# merge

```text
Operator:       merge
Stream shape:   A* → (A | B)*
State:          Bool × Bool
Initial state:  (false, false)
Value step:     (S × InputNext<A, B>) → (S × (A | B)) — forward the arriving payload
Ownership:      One state and two input connections per operator subscription
Lifecycle:      Complete after both inputs; any error closes both; cancellation closes both silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `merge(other)`; one Observable `B*`, unlimited concurrency, no scheduler · **Format:** Compact

> Values from the source and the supplied stream flow together in arrival order.

## Notation and scope

- `S` holds the two input-completed flags. `InputNext<A, B> = FromSource(A) | FromOther(B)` identifies which input delivered the value; no payload is combined or buffered.
- Setup subscribes to the source and then the supplied input while open. Synchronous emissions can occur during setup; an early error can prevent later subscriptions.
- The fixed `other: B*` argument is omitted from the left side of Stream shape. More inputs generalize the completion flags; finite concurrency and scheduler overloads are outside this scope.
- This card covers deprecated `merge` from `rxjs/operators`, not the creation function exported from `rxjs`. The modern pipeable counterpart is [mergeWith](mergeWith.md).
- There is no added sharing or default scheduling. Each input connection follows that input's own execution policy.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/merge.ts)
- [Merge coordination](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
