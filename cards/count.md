# count

```text
Operator:       count
Stream shape:   T* → Nat?
State:          Nat × Nat — matching count and source index
Initial state:  (0, 0)
Value step:     (S × T) → (S × Action[]) — increment count when accepted; emit nothing yet
Ownership:      One state per operator subscription
Lifecycle:      Completion emits count including zero; errors forward; cancellation closes without a count
```

**Family:** Aggregation · **Reference:** RxJS 7.8.2 · **Scope:** count(predicate?), with optional index-aware predicate · **Format:** Compact

> Count matching source values and emit the total after normal completion.

## Notation and scope

Without a predicate, count every value. With a predicate, pass the value and zero-based source index; the index advances even for rejected values.

Normal empty completion emits zero then completes. Source/predicate errors end without a count, and cancellation closes upstream silently. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/count.ts)
- [RxJS 7.8.2 reduce implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/reduce.ts)
- [RxJS 7.8.2 scanInternals implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/scanInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
