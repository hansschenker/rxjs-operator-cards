# concatMap

```text
Operator:       concatMap
Stream shape:   T* → R*
State:          Option<InnerId> × T[] × Nat × Bool
Initial state:  (None, [], 0, false)
Value step:     (S × T) → (S × Action[]) — connect if idle; queue the outer value if busy
Ownership:      One state per operator subscription
Lifecycle:      Source complete drains work; errors forward and close all; cancellation closes and discards the queue silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `concatMap(project)`; no deprecated `resultSelector`.

> Source values wait their turn; each inner must complete before the next begins, preserving admission order.

## Type shape

```text
concatMap :: (T → R*) → T* → R*
```

The projection describes an inner Observable. This signature omits its index and simplifies `ObservableInput` to Observable. Queueing governs projection timing.

## Intuition

Edits must be saved in arrival order. While one save is active, subsequent edits wait. The projection defines saving; `concatMap` starts each save after its predecessor completes.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | If idle, project and connect immediately; otherwise enqueue the outer value without projecting it yet. |
| Whole connection | `T* → R*`; inner values appear in admitted-source order, with one active inner at a time. |

An inner may emit no values or many. Earlier inners must complete before queued values are admitted; eventual output is not guaranteed.

## Remembers

Each connection owns a FIFO queue, current inner subscription, active count, projection index, and source-completed flag. RxJS 7.8.2 implements this through `mergeMap` with concurrency one.

The compact state lists the optional current inner identity, FIFO queue of outer values, next projection index, and source-completed flag, in that order. An occupied inner slot represents an active count of one. Queueing a value does not advance the index; admitting it to the projection does.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize an empty queue and connect to the source. |
| Source next while idle | Reserve the slot, project this value, and connect to its inner. |
| Source next while busy | Append the outer value to the queue. |
| Inner next | Forward the value. |
| Inner complete | Release the slot; admit the oldest queued value, or complete if the source is complete and the queue is empty. |
| Source complete | Remember completion; finish the current inner and drain the queue. |
| Source error | Error downstream, close the inner, and abandon queued work. |
| Projection or inner error | Error downstream, close owned connections, and abandon queued work. |
| Downstream close | Close source and inner; queued values receive no further projection. |

## Time, cancellation, and sharing

- **Time:** Sources and their schedulers determine timing. Waiting ends on inner completion, not after a built-in duration.
- **Cancellation:** New values preserve current work. Closing the result runs owned teardown without sending `complete` or undoing completed effects; external cancellation depends on the producer.
- **Sharing:** Each downstream connection owns its queue and inner connection. Sharing the source alone does not share those queues.

## Worked trace

Illustrative times are milliseconds from the source connection. The cold source emits A at 0, B at 20, and completes at 30. Each cold inner emits its label 50 ms after connection, then completes. Within a row, `next` precedes `complete`.

| Time | Event | Output and state |
| --- | --- | --- |
| 0 | Source A | Project and connect A. |
| 20 | Source B | Queue B; do not project B yet. |
| 30 | Source complete | Continue A with B waiting. |
| 50 | A next, complete | Emit A; project and connect B. |
| 100 | B next, complete | Emit B, then complete. |

## Verification obligations

- Check the trace and that B's projection occurs at 50.
- Check that an empty inner releases its slot without output.
- Check that a never-completing inner prevents queued admission.
- Check that error or closure abandons queued work.

## Common confusion

The queue holds outer values, not already-created inner Observables. `map(project)` followed by `concatAll()` projects eagerly and can start external effects earlier. A fast source can also grow the queue indefinitely; serial admission does not slow that source.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/concatMap)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/concatMap.ts)
- [RxJS 7.8.2 merge machinery](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
