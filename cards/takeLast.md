# takeLast

```text
Operator:       takeLast
Stream shape:   T* → T* (at most n values)
State:          T[] — at most n newest values
Initial state:  []
Value step:     (T[] × T) → (T[] × Action[]) — retain newest n; emit nothing yet
Ownership:      One state per operator subscription
Lifecycle:      n = 0 skips source; completion flushes then completes; error/cancel discards pending values
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** takeLast(n), with a nonnegative integer n · **Format:** Compact

> Remember the newest n values and deliver them in source order when the source completes.

## Notation and scope

The array is a bounded queue, not an inner Observable. Each new value replaces the oldest retained value once the queue reaches n.

For n > 0, normal source completion releases the retained values synchronously and then completes, while downstream remains open. Error forwards without flushing; cancellation closes upstream without a terminal notification.

For n = 0, the result completes without subscribing to the source. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeLast.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
