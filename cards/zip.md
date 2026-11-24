# zip

```text
Operator:       zip
Stream shape:   A* → (A × B)*
State:          A[] × B[] × Bool × Bool
Initial state:  ([], [], false, false)
Value step:     (S × InputNext<A, B>) → (S × Action[]) — enqueue, emit a ready pair, and check exhaustion
Ownership:      One FIFO per input and two connections per operator subscription
Lifecycle:      Complete when any completed input has no queued value; any error closes all; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `zip(other)`; one Observable `B*`, no result selector · **Format:** Compact

> The first value from each input forms the first pair, then the second values form the second pair.

## Notation and scope

- `S` holds the two FIFO queues and their completed flags. `InputNext<A, B> = FromSource(A) | FromOther(B)`; `Action[]` can include a pair emission followed by completion.
- A next appends to its own queue. If both queues are nonempty, remove one head from each and emit them together; a faster input may accumulate a growing queue.
- A completed input may still supply its queued values. As soon as its queue is empty, no further pair is possible, so complete and close the other connection.
- This is deprecated pipeable `zip` from `rxjs/operators`, distinct from root creation `zip`. Prefer [zipWith](zipWith.md); array and projection overloads are omitted.
- No scheduler or sharing is added. An error discards queued values; cancellation performs the same disposal without a terminal notification.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/zip.ts)
- [Zip implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/zip.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
