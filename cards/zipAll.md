# zipAll

```text
Operator:       zipAll
Stream shape:   (T*)* → (T[])*
State:          Collect((T*)[]) | Join(ZipJoin<T>)
Initial state:  Collect([])
Value step:     (S × T*) → (S × Action[]) — append while collecting; no downstream value yet
Ownership:      One collected list, then one join execution, per operator subscription
Lifecycle:      Outer complete starts the join; join controls final completion; any error or cancellation closes owned work
```

**Family:** Combination · **Reference:** RxJS 7.8.2 · **Scope:** `zipAll()`; Observable-valued outer, no projection · **Format:** Compact

> Collect all inner descriptions until the outer completes, then pair their values by position.

## Notation and scope

- `Collect(inputs)` owns a finite array of inner descriptions. No inner is subscribed before outer completion; an endless outer never reaches the join phase.
- `ZipJoin<T>` holds one `T[]` FIFO and completed flag per collected input. When every queue is nonempty, remove one head from each and emit their array.
- The join completes when any completed input has an empty queue, since no further full tuple can be formed; remaining input connections are then closed.
- An empty collected list completes without a value. Outer error skips the join; any join error propagates immediately. Cancellation is silent and closes all currently owned connections.
- The projection overload is omitted. `T[]` is one emitted array payload; `(T[])*` is an Observable of those arrays.
- No scheduler or sharing is introduced. Collected inputs keep their own cold or shared execution behavior when eventually subscribed.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/zipAll.ts)
- [Collect-then-join helper](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/joinAllInternals.ts)
- [Join implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/zip.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
