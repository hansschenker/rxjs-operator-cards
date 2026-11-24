# mergeMap

```text
Operator:       mergeMap
Stream shape:   T* → R*
State:          InnerId[] × Nat × Bool
Initial state:  ([], 0, false)
Value step:     (S × T) → (S × Action[]) — open an inner; keep existing inners
Ownership:      One state per operator subscription
Lifecycle:      Source complete waits for all inners; errors forward and close all; cancellation closes silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `mergeMap(project)` with unbounded concurrency; no deprecated `resultSelector`.

> Each source value opens an inner stream, and values from all active inners flow onward as they arrive, allowing work to overlap.

## Type shape

```text
mergeMap :: (T → R*) → T* → R*
```

The projection describes an inner Observable for one source value. This explanatory signature omits its index and simplifies `ObservableInput` to Observable. The merge policy combines the nested streams.

## Intuition

Independent download requests arrive over time. Start each immediately and report every result. The projection supplies download behavior; `mergeMap` supplies the overlap policy.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Call the projection and connect to its inner stream immediately. Existing inners remain connected. |
| Whole connection | `T* → R*`; each inner can emit zero, one, or many values. Output follows delivery order, not source order. |

One input does not guarantee one output. Errors, empty inners, and downstream closure can prevent results.

## Remembers

Each downstream connection owns an active-inner count, inner subscriptions, projection index, and source-completed flag. The implementation's waiting queue remains unused under this unbounded policy.

In the compact state, `InnerId[]` identifies the active inner subscriptions, `Nat` is the next projection index, and `Bool` records source completion. The active count is the length of the list. These are symbolic subscription identities, not buffered inner values.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize state and connect to the source. |
| Source next | Reserve an active slot, call the projection, and connect to the returned inner. |
| Inner next | Forward its value immediately. |
| Inner complete | Release its slot; complete downstream if the source has completed and no inner remains. |
| Source complete | Remember completion; wait for all active inners. |
| Source error | Error downstream and close all owned connections. |
| Projection or inner error | Error downstream and close the source and other inners. |
| Downstream close | Close the source and every active inner; stop delivery. |

## Time, cancellation, and sharing

- **Time:** Source and inner producers, with their schedulers, determine delivery times. `mergeMap` adds no timer or scheduler.
- **Cancellation:** New inputs do not cancel existing inners. Closing the result runs owned teardown; it neither sends `complete` nor reverses completed effects. An external producer stops only if its teardown supports that.
- **Sharing:** No sharing is introduced. Two downstream connections maintain separate projection and inner state, even if the source itself is shared.

## Worked trace

Illustrative milliseconds are measured from the source connection. A cold source emits A at 0, B at 20, and completes at 30. Each cold inner emits its label 50 ms after its own connection, then immediately completes. Within a row, `next` precedes `complete`.

| Time | Event | Output and state |
| --- | --- | --- |
| 0 | Source A | Connect A; one active inner. |
| 20 | Source B | Connect B; two active inners. |
| 30 | Source complete | Wait for A and B. |
| 50 | A next, complete | Emit A; B remains. |
| 70 | B next, complete | Emit B, then complete. |

## Verification obligations

- Check the worked trace and both overlapping inner lifetimes.
- Check that a later, faster inner may emit first.
- Check that source completion waits, while any error closes remaining inners.
- Check that downstream closure prevents subsequent deliveries.

## Common confusion

Overlap does not imply a new thread. The finite-concurrency variant accepts a separate positive integer limit: excess **outer values** queue, and their projections run only when slots open. That differs operationally from eagerly applying `map(project)` before `mergeAll(limit)`.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/mergeMap)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeMap.ts)
- [RxJS 7.8.2 merge machinery](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
