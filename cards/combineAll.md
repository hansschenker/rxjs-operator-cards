# combineAll

```text
Operator:       combineAll
Stream shape:   (T*)* → (T[])*
State:          Collect((T*)[]) | Join(LatestJoin<T>)
Initial state:  Collect([])
Value step:     (S × T*) → (S × Action[]) — append while collecting; no downstream value yet
Ownership:      One collected list, then one join execution, per operator subscription
Lifecycle:      Outer complete starts the join; join controls final completion; any error or cancellation closes owned work
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `combineAll()`; Observable-valued outer, no projection · **Format:** Compact

> Collect all inner descriptions until the outer completes, then emit arrays of their latest values.

## Notation and scope

- `Collect(inputs)` owns a finite array of inner descriptions. No inner is subscribed before outer completion; an endless outer never reaches the join phase.
- `LatestJoin<T>` holds one `Option<T>` latest slot and completed flag per collected input. Once all slots are populated, any input next emits a new `T[]` containing the latest values.
- The 7.8.2 join completes only when all inputs complete. An input completing empty prevents output, but does not end an otherwise unfinished join.
- An empty collected list completes without a value. Outer error skips the join; any join error propagates immediately. Cancellation is silent and closes all currently owned connections.
- `combineAll` is the deprecated alias of [combineLatestAll](combineLatestAll.md), available through `rxjs/operators` and the compatibility exports in `rxjs`.
- No scheduler or sharing is introduced. Collected inputs keep their own cold or shared execution behavior when eventually subscribed.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/combineAll.ts)
- [Collect-then-join helper](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/joinAllInternals.ts)
- [Join implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/combineLatest.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
