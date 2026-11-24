# partition

```text
Operator:       partition
Stream shape:   T* → (T* × T*)
State:          Nat — separately for each branch subscription
Initial state:  0
Value step:     (Nat × T) → (Nat × Option<T>) — increment index and emit only when this branch matches
Ownership:      Independent source connection and predicate index per branch subscription
Lifecycle:      Each branch forwards its own completion/error; canceling one branch closes only that connection
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** pipeable `partition(predicate)`; no `thisArg` · **Format:** Compact

> Return two stream descriptions: one accepts matching values, and the other accepts nonmatching values.

## Notation and scope

- This is deprecated `partition` from `rxjs/operators`, a function returning a pair of Observables. Root `rxjs` exports a separate creation function with the source as an explicit argument.
- The first branch uses `predicate(value, index)`; the second negates it. Each subscribed branch advances its own zero-based index on every source next.
- The operator constructs two filters; it does **not** introduce a shared source subscription or evaluate the predicate just once for both branches. Subscribing to both cold branches executes the source twice.
- A predicate failure errors that branch. To share production, explicitly share the source before partitioning; each branch still owns its filter state and predicate calls.
- No scheduler is added. The pair in Stream shape is a returned pair of stream descriptions, not a stream emitting pairs of values.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/partition.ts)
- [Filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [Predicate negation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/util/not.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
