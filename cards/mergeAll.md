# mergeAll

```text
Operator:       mergeAll
Stream shape:   (T*)* → T*
State:          InnerId[] × Bool
Initial state:  ([], false)
Value step:     (S × T*) → (S × Action[]) — connect every arriving inner
Ownership:      One state and owned inner connections per operator subscription
Lifecycle:      Outer complete waits for all inners; any error closes all; cancellation closes silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `mergeAll()`; Observable-valued outer, unlimited concurrency · **Format:** Compact

> Every arriving inner stream is connected, and their values flow together as they arrive.

## Notation and scope

- `S` contains the active inner identities and the outer-completed flag. `Action[]` records connection decisions; an inner next forwards its value.
- Each inner completion removes that connection. Completion requires both a completed outer and no active inners.
- The finite-concurrency overload also queues inner descriptions; that queue is outside this unlimited-concurrency state.
- No scheduler or sharing is introduced. Inner streams may emit synchronously, and each connection follows its source's existing cold or shared behavior.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeAll.ts)
- [Merge coordination](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
