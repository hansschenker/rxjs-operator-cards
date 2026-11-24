# mergeScan

```text
Operator:       mergeScan
Stream shape:   T* → R*
State:          R × InnerId[] × Nat × Bool
Initial state:  (seed, [], 0, false)
Value step:     (S × T) → (S × Action[]) — call accumulator with current state and index; connect its inner
Ownership:      One accumulator cell and active-inner set per operator subscription
Lifecycle:      Outer complete waits for all inners; errors close all; cancellation closes silently
```

**Family:** Recursive accumulation · **Reference:** RxJS 7.8.2 · **Scope:** `mergeScan(accumulator, seed)`; unlimited concurrency, Observable results · **Format:** Compact

> Each source value starts an inner stream, and every inner result becomes the next remembered state.

## Notation and scope

- `S` contains the latest accumulated `R`, active inner identities, next projection index, and outer-completed flag.
- The accumulator receives `(currentState, sourceValue, index)`. Calling it does not itself update the stored `R`; **each inner next** first replaces that cell and then emits its value.
- Concurrent inners may interleave and overwrite the cell in arrival order. A later source value reads the most recently emitted inner value, or `seed` if no inner has emitted.
- The seed is not emitted by itself and is not cloned. With finite concurrency, queued outer values call the accumulator only when a slot opens; that queue is outside this default scope.
- There is no added scheduling or sharing. Inner completion releases a slot; source or inner errors, including accumulator failure, terminate the result.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeScan.ts)
- [Merge coordination](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
