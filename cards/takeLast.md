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

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** takeLast(n), with a nonnegative integer n · **Format:** Detailed

> Remember the newest n values and deliver them in source order when the source completes.

## Type shape

```text
takeLast :: Nat → T* → T*
takeLast(1) :: T* → T?
```

The fixed count `n` bounds the retained suffix and eventual output count. `T?` for count one is an upper bound, not a promise that a value or completion will arrive. These function-first signatures omit no scheduling policy: this scoped operation has no scheduler argument.

## Intuition

A finite measurement run produces many readings, but its report needs only the final two. While the run continues, keep replacing older candidates. Only normal completion establishes which readings were actually last, allowing them to be published in their original order.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source next, `n > 0` | Retain the newest `n` values and produce no downstream value yet. |
| Normal source completion | Emit the retained values individually in source order, then complete, while downstream stays open. |
| Whole connection | With `k` source values and normal completion, an uninterrupted result contains `min(n, k)` values. |

For `n > 0`, a non-completing source produces no output even after its buffer fills. An empty source completes without a value. Source error or early closure discards the pending suffix. For `n = 0`, immediate empty completion does not require a source subscription.

## Remembers

`T[]` is an ordinary in-memory queue containing at most `n` payloads. Append a new value; if the queue is too large, remove its oldest member. `Action[]` in the source-value step is empty because publication belongs to the completion event.

Each connection owns its queue. Payload objects are retained by reference, not deeply copied. The queue is neither an inner Observable nor the output payload: its contents are later emitted as separate values. The zero-count branch bypasses this buffering execution.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect, `n = 0` | Complete immediately without subscribing upstream. |
| Connect, `n > 0` | Initialize an empty queue and connect upstream. |
| Source next | Append the value and remove the oldest if necessary; emit nothing. |
| Source complete | Flush the retained suffix in order, complete if still open, and release the connection and buffer. |
| Source error | Error downstream without flushing; release the connection and buffer. |
| Downstream close | Close upstream and discard retained values without a terminal notification. |

Closure during flushing also prevents remaining values and completion from reaching that observer.

## Time, cancellation, and sharing

- **Time:** Source completion triggers a synchronous flush. Stored arrival times are not replayed: all retained values are delivered during completion handling, without a new timer or scheduler.
- **Cancellation:** Early closure releases owned subscriptions and pending payload references. It does not reverse effects already performed by the source; external work stops only if teardown supports it.
- **Sharing:** No sharing is introduced. Separate connections retain separate suffixes, including when the source is already shared.

## Worked trace

Illustrative cold source, connected before its first value at time zero. Times are milliseconds from connection. Apply `takeLast(2)`; the three actions in the final row occur in the displayed order.

| Time | Source event | Retained queue | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | `[1]` | None |
| 10 | `next(2)` | `[1, 2]` | None |
| 20 | `next(3)` | `[2, 3]` | None |
| 30 | `next(4)` | `[3, 4]` | None |
| 40 | `next(5)` | `[4, 5]` | None |
| 50 | `complete` | Released | `next(4)`, `next(5)`, `complete` |

## Verification obligations

- Check output waits for normal completion and retains suffix order.
- Check zero count skips source subscription, including an erroring source.
- Check empty/short completion and failure with a populated buffer.
- Check cancellation before completion and during the synchronous flush.
- Check independent buffers for separate connections.

These obligations are broader than any single recorded validation run.

## Common confusion

Filling the buffer does not release it; further arrivals can replace its candidates indefinitely. The output also contains individual values, unlike `buffer`, whose output contains arrays. At count zero, this operator completes without observing even a source error because that source is never connected.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/takeLast)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeLast.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
