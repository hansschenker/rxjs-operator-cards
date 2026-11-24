# last

```text
Operator:       last
Stream shape:   T* → T?
State:          Option<T> × Nat — latest match and predicate index
Initial state:  (None, 0)
Value step:     (S × T) → (S × Action[]) — replace candidate on a match; emit nothing yet
Ownership:      One state per operator subscription
Lifecycle:      Completion emits latest match or errors if absent; source errors forward; cancellation discards silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** last(predicate), without a default value · **Format:** Compact

> Remember the latest matching value and publish it only when the source completes.

## Notation and scope

The predicate receives value, source index, and source Observable. Without a predicate, every source value replaces the candidate. Option distinguishes no candidate from an actual null or undefined value.

Normal completion emits the candidate and completes, or sends EmptyError if none exists. A supplied second default argument replaces that empty-result error with a default emission. Predicate errors and source errors discard the candidate.

An endless source has no final result. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/last.ts)
- [RxJS 7.8.2 takeLast implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeLast.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 throwIfEmpty implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
