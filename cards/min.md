# min

```text
Operator:       min
Stream shape:   T* → T?
State:          Option<T> — smallest candidate so far
Initial state:  None
Value step:     (Option<T> × T) → (Option<T> × Action[]) — retain the smaller candidate
Ownership:      One state per operator subscription
Lifecycle:      Completion emits candidate if present; empty completion stays empty; errors/cancel discard candidate
```

**Family:** Aggregation · **Reference:** RxJS 7.8.2 · **Scope:** min(comparer?), comparator returns a numeric ordering result · **Format:** Compact

> Remember the smallest candidate and emit it when the source completes.

## Notation and scope

The first value becomes the candidate without comparison. A supplied comparer(old, current) keeps old only when its result is negative; otherwise current replaces it, including on ties. Without a comparer, use JavaScript old < current.

An empty source completes without a value. Source/comparer failures forward error; cancellation closes upstream silently. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/min.ts)
- [RxJS 7.8.2 reduce implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/reduce.ts)
- [RxJS 7.8.2 scanInternals implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/scanInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
