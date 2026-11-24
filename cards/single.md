# single

```text
Operator:       single
Stream shape:   T* → T?
State:          Option<T> × Bool × Nat — candidate, any-source-value flag, predicate index
Initial state:  (None, false, 0)
Value step:     (S × T) → (S × Action[]) — retain first match; error on second match
Ownership:      One state per operator subscription
Lifecycle:      Completion emits sole match; missing/extra matches error; cancellation closes without a result
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** single(predicate), with optional predicate · **Format:** Compact

> Require exactly one matching source value, and publish it after the source completes.

## Notation and scope

Bool records whether any source value arrived, even if rejected. Nat counts predicate calls when a predicate is supplied. Without a predicate, every source value matches.

A second match sends SequenceError immediately. On completion, one match emits then completes; an empty source sends EmptyError; a nonempty source with no match sends NotFoundError.

Source/predicate errors forward, and cancellation closes upstream silently without emitting the candidate. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/single.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
