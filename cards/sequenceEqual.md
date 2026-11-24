# sequenceEqual

```text
Operator:       sequenceEqual
Stream shape:   T* → Bool? (compareTo: T* supplied)
State:          T[] × T[] × Bool × Bool — unmatched queues and completion flags
Initial state:  ([], [], false, false)
Value step:     (S × (Left(T) | Right(T))) → (S × Action[]) — queue or compare oldest unmatched values
Ownership:      One two-source comparison state per operator subscription
Lifecycle:      Mismatch emits false early; both completed and matched emit true; errors forward; cancel closes both silently
```

**Family:** Sequence comparison · **Reference:** RxJS 7.8.2 · **Scope:** sequenceEqual(compareTo, comparator?), Observable comparison input · **Format:** Compact

> Compare the two value sequences by position, ignoring differences in arrival time.

## Notation and scope

Left(T) and Right(T) tag a value from the source or compareTo. The queues retain values that arrived before their partner; the flags record each input’s completion. Source is subscribed before compareTo.

When a partner exists, compare the newly arriving value with the oldest queued value from the other side. A custom comparator should account for this arrival-dependent argument order; the default is ===.

A value mismatch or an arrival beyond an already completed empty opposite side emits false and completes. Once both inputs complete, leftover unmatched values mean false and equal-length matched sequences mean true. An unmatched suffix can remain pending until the other side completes or another value proves inequality.

Source/comparator errors do not emit a boolean result. No scheduler or sharing is introduced; one faster source can cause its queue to grow.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/sequenceEqual.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
