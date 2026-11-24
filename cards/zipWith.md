# zipWith

```text
Operator:       zipWith
Stream shape:   A* → (A × B)*
State:          A[] × B[] × Bool × Bool
Initial state:  ([], [], false, false)
Value step:     (S × InputNext<A, B>) → (S × Action[]) — enqueue, emit a ready pair, and check exhaustion
Ownership:      One FIFO per input and two connections per operator subscription
Lifecycle:      Complete when any completed input has no queued value; any error closes all; cancellation closes silently
```

**Family:** Combination · **Reference:** RxJS 7.8.2 · **Scope:** `zipWith(other)`; one Observable `B*`, no result selector · **Format:** Compact

> The first value from each input forms the first pair, then the second values form the second pair.

## Notation and scope

- `S` holds the two FIFO queues and their completed flags. `InputNext<A, B> = FromSource(A) | FromOther(B)`; `Action[]` can include a pair emission followed by completion.
- A next appends to its own queue. If both queues are nonempty, remove one head from each and emit them together; a faster input may accumulate a growing queue.
- A completed input may still supply its queued values. As soon as its queue is empty, no further pair is possible, so complete and close the other connection.
- The supplied `other: B*` is fixed. More inputs add one queue and completed flag each.
- No scheduler or sharing is added. An error discards queued values; cancellation performs the same disposal without a terminal notification.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/zipWith.ts)
- [Zip implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/zip.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
