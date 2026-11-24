# combineLatest

```text
Operator:       combineLatest
Stream shape:   A* → (A × B)*
State:          Option<A> × Option<B> × Bool × Bool
Initial state:  (None, None, false, false)
Value step:     (S × InputNext<A, B>) → (S × Option<A × B>) — update that slot; emit once both are present
Ownership:      One latest-value cache and two input connections per operator subscription
Lifecycle:      Complete after both inputs; any error closes both; cancellation closes both silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `combineLatest(other)`; one Observable `B*`, no result selector · **Format:** Compact

> Once each input has supplied a value, every new input value produces the latest pair.

## Notation and scope

- `S` contains the latest source value, latest other value, and their completed flags. `InputNext<A, B> = FromSource(A) | FromOther(B)`; `None` in the step means no emission.
- The fixed `other: B*` argument is omitted from Stream shape. A completed input keeps its last value available while the other continues.
- **RxJS 7.8.2 implementation detail:** an input completing empty prevents all pairs, but the result still waits for every input to complete. Empty plus a never-completing input therefore remains silent and open.
- This covers deprecated pipeable `combineLatest` from `rxjs/operators`, not the root creation function. Prefer [combineLatestWith](combineLatestWith.md); array and result-selector overloads are omitted.
- Inputs are subscribed in argument order and may emit synchronously. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/combineLatest.ts)
- [Latest-value join implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/combineLatest.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
