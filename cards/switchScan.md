# switchScan

```text
Operator:       switchScan
Stream shape:   T* → R*
State:          R × Option<InnerId> × Nat × Bool
Initial state:  (seed, None, 0, false)
Value step:     (S × T) → (S × Action[]) — close old inner, then accumulate and connect the replacement
Ownership:      One accumulator cell and current inner per operator subscription
Lifecycle:      Outer complete waits for current inner; errors close all; cancellation closes silently
```

**Family:** Recursive accumulation · **Reference:** RxJS 7.8.2 · **Scope:** `switchScan(accumulator, seed)`; Observable results · **Format:** Compact

> Each source value replaces the current inner stream, whose emitted values update the remembered state.

## Notation and scope

- `S` contains the latest accumulated `R`, optional current inner identity, next projection index, and outer-completed flag.
- Close the old inner before calling `accumulator(currentState, value, index)`. **Only a current-inner next** replaces the accumulated value and emits it downstream.
- A replacement that has not emitted leaves the state at its previous value. Source arrivals and inner completion do not independently advance the accumulator.
- The seed is not emitted by itself or cloned. Inner completion clears the slot; source completion waits for that slot to become empty.
- No scheduler or sharing is added. Cancellation stops future delivery from the source and current inner; it does not retract prior outputs.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchScan.ts)
- [Switch implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
