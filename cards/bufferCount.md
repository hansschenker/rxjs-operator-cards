# bufferCount

```text
Operator:       bufferCount
Stream shape:   T* → (T[])*
State:          T[] — current batch, shorter than n
Initial state:  []
Value step:     (T[] × T) → (T[] × Option<T[]>) — append; publish/reset at size n
Ownership:      One state per operator subscription
Lifecycle:      Completion flushes a nonempty remainder then completes; errors forward; cancel discards silently
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** bufferCount(n), positive integer n and default nonoverlapping start interval · **Format:** Compact

> Publish consecutive arrays of n values, plus the final incomplete batch on normal completion.

## Notation and scope

T[] is one finite array payload. A full batch is emitted on the source value that fills it; the next batch starts with the next value. Empty source completion emits no array in this scope.

Error or cancellation discards an incomplete batch. The optional startBufferEvery argument permits overlapping or gapped batches and requires multiple-buffer state; it is outside this compact scope.

Counting follows source arrivals; no timer, notifier, or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/bufferCount.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
