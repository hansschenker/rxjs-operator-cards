# switchMap

```text
Operator:       switchMap
Stream shape:   T* → R*
State:          Option<InnerId> × Nat × Bool
Initial state:  (None, 0, false)
Value step:     (S × T) → (S × Action[]) — close old inner, then project and connect the replacement
Ownership:      One state per operator subscription
Lifecycle:      Source complete waits for current inner; errors forward and close all; cancellation closes silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `switchMap(project)`; no deprecated `resultSelector`.

> Each source value replaces the current inner connection, so subsequent results come from the newest selected stream.

## Type shape

```text
switchMap :: (T → R*) → T* → R*
```

The projection describes an inner Observable. This explanatory signature omits its index and simplifies `ObservableInput` to Observable. The replacement policy selects which inner delivers values.

## Intuition

Search text changes while earlier searches are running. Each new text selects a replacement result stream. Disconnect the previous stream so stale future results cannot update the view through it.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Unsubscribe the previous inner, then call the projection and connect to the new inner. |
| Whole connection | `T* → R*`; forward values from the current inner, which may emit zero, one, or many. |

Replacement controls future delivery. It neither retracts delivered values nor guarantees that the newest inner produces a result.

## Remembers

Each downstream connection owns the current inner subscription, projection index, and source-completed flag. Superseded inputs and previous outputs are not retained.

The compact state lists the optional current inner identity, next projection index, and source-completed flag, in that order. `InnerId` identifies a subscription, not an emitted value. The value-step actions preserve closing-before-projection order.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Start without an inner and connect to the source. |
| Source next | Close the previous inner before projecting; connect to the returned replacement. |
| Current inner next | Forward its value. |
| Current inner complete | Clear the current inner; complete downstream if the source has completed. |
| Source complete | Remember completion; wait for the current inner, or complete immediately when none is active. |
| Source error | Error downstream and close the current inner. |
| Projection error | Error downstream; the previous inner has already been closed. |
| Current inner error | Error downstream and close the source. |
| Downstream close | Close source and current inner; stop delivery. |

## Time, cancellation, and sharing

- **Time:** Source and inner producers supply delivery timing through their schedulers. Replacement happens on source `next`; no timer is added.
- **Cancellation:** Replacement unsubscribes the old inner and runs its teardown. This does not send it `complete`, undo an external effect, or necessarily abort external work. A shared producer may continue for other consumers.
- **Sharing:** No sharing is introduced. Each downstream connection owns its own current inner, even when connected to a shared source.

## Worked trace

Illustrative milliseconds are measured from the source connection. A cold source emits A at 0, B at 20, and completes at 30. Each cold inner emits its label 50 ms after its own connection, then immediately completes; its timer is canceled on teardown. Within a row, `next` precedes `complete`.

| Time | Event | Output and state |
| --- | --- | --- |
| 0 | Source A | Project and connect A. |
| 20 | Source B | Cancel A; project and connect B. |
| 30 | Source complete | Wait for B. |
| 50 | A's former deadline | No event: A's timer was canceled. |
| 70 | B next, complete | Emit B, then complete. |

## Verification obligations

- Check the trace and A's teardown at 20.
- Check teardown occurs before the replacement projection.
- Check source completion waits for the current inner.
- Check a synchronous inner may emit several values before any replacement arrives.

## Common confusion

“Only latest” does not mean only one value per input. Also, `map(project)` followed by `switchAll()` calls the projection before canceling the previous inner; `switchMap` cancels first. That ordering matters when projection or teardown has effects or throws.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/switchMap)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchMap.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
