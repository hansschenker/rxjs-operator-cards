# concatAll

```text
Operator:       concatAll
Stream shape:   (T*)* → T*
State:          Option<InnerId> × (T*)[] × Bool
Initial state:  (None, [], false)
Value step:     (S × T*) → (S × Action[]) — connect when idle; otherwise enqueue
Ownership:      One queue and current inner connection per operator subscription
Lifecycle:      Outer complete drains accepted inners; any error closes all; cancellation discards the queue silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `concatAll()`; Observable-valued outer · **Format:** Compact

> Inner streams take turns: the next queued stream starts after the current one completes.

## Notation and scope

- `S` contains the current inner identity, the FIFO of inner descriptions, and the outer-completed flag. `(T*)[]` is a finite queue of streams, not a stream of outputs.
- Inner values forward immediately. A normal inner completion opens the oldest queued stream, or completes the result if the outer is done and the queue is empty.
- A never-ending inner prevents later queued subscriptions. Queuing retains descriptions; it does not connect those streams early.
- This is `mergeAll(1)`. It adds neither a scheduler nor sharing; cancellation closes the outer and current inner.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/concatAll.ts)
- [Merge coordination](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
