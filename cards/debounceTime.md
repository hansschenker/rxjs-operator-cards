# debounceTime

```text
Operator:       debounceTime
Stream shape:   T* → T*
State:          Option<T × Time> × Option<TimerId> — latest arrival and scheduled task
Initial state:  (None, None)
Value step:     (S × T × Time) → (S × Action[]) — replace pending value; schedule if idle
Ownership:      One pending value and scheduled task per operator subscription
Lifecycle:      Completion flushes then completes; error discards then errors; cancellation discards and closes silently
```

**Family:** Time · **Reference:** RxJS 7.8.2 · **Scope:** Positive duration with an asynchronous or virtual-time scheduler that honors scheduled deadlines.

> Remember the newest value and release it after a quiet interval, while giving completion and failure their own rules.

## Type shape

```text
debounceTime :: Duration → Scheduler → (T* → T*)
```

The duration and scheduler select the waiting rule and clock. This function-first notation makes the scheduler visible; the actual API accepts both arguments together and defaults to `asyncScheduler`.

## Intuition

A text field produces query values as someone types. Each new query replaces the pending one. Once typing pauses for the chosen duration, the latest query continues downstream.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source value | Becomes the pending candidate; it may be replaced before emission |
| Whole connection | `T* → T*`; output values preserve source order but can be omitted and delayed |

The operator does not promise an output for every burst if the connection errors or closes. Completion can release a pending value before its deadline.

## Remembers

In the compact header, `S` is the state shown above, `Time` is read from the supplied scheduler when the value arrives, and `TimerId` identifies the owned scheduled action. An absent pending value has no relevant arrival time.

Per downstream connection: the latest value, the time of its arrival, and an active scheduled action. The behavior can be described by a moving deadline. The implementation may reuse/reschedule an existing action rather than canceling and allocating a timer on every value.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize empty pending state and connect to the source |
| Source next | Replace the pending value; set the target deadline from the scheduler clock |
| Scheduled check before the current deadline | Reschedule for the remaining interval |
| Current deadline reached | Clear pending state and emit its remembered value |
| Source complete | Emit a pending value immediately, if present, then complete |
| Source error | Discard the pending value, forward the error, and release work |
| Downstream close | Close upstream, release scheduled work, and clear state without sending `complete` |

## Time, cancellation, and sharing

- **Time:** The scheduler supplies `now()` and scheduling. Duration is interpreted in its clock units. A busy real event loop can deliver later than the nominal deadline.
- **Cancellation:** Scheduled work and the source connection are owned by this execution. Closing prevents later delivery; it does not retract earlier values.
- **Sharing:** No sharing is introduced. Independent downstream connections have independent pending state, even if they listen to the same shared source.

## Worked trace

Use a cold input, duration 50, and a virtual clock measured from connection. No source event ties a deadline.

| Time | Event | Output |
| --- | --- | --- |
| 0 | A arrives | None; deadline 50 |
| 20 | B arrives | None; deadline 70 |
| 40 | C arrives | None; deadline 90 |
| 90 | Quiet interval reached | C |
| 120 | D arrives | None; deadline 170 |
| 170 | Quiet interval reached | D |
| 200 | Source completes | Complete |

If the source instead completes at 140, D is emitted at 140 immediately before completion. If it errors or downstream closes at 140, D is discarded.

## Verification obligations

- A new value postpones eligibility and replaces the earlier candidate.
- Normal completion flushes pending state; error and closure do not.
- A canceled scheduled action cannot emit later through the closed subscriber.
- Distinct connections do not share pending values accidentally.

## Common confusion

The output has the same `T*` shape as throttling, but admission rules differ. Also, a blanket claim of at most one output in every duration-sized interval misses completion flushing: a final flush can occur sooner after a previous output.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/debounceTime)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/debounceTime.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
