# sampleTime

```text
Operator:       sampleTime
Stream shape:   T* → T*
State:          Option<T> × TimerId — pending value and periodic timer
Initial state:  (None, periodic timer created during connection setup)
Value step:     (S × T) → (S × Action[]) — replace pending value; emit nothing yet
Ownership:      One pending value and periodic timer per operator subscription
Lifecycle:      Source complete discards pending; errors forward; cancellation cancels timer and closes silently
```

**Family:** Time sampling · **Reference:** RxJS 7.8.2 · **Scope:** Positive period; asynchronous or virtual scheduler · **Format:** Compact

> At periodic scheduler ticks, emit the latest value received since the preceding tick.

## Notation and scope

- The periodic timer is created through `interval` after source subscription. A tick with a pending value clears the pending state before emitting the saved value; no fresh source value means no output for that tick.

- Source completion does not add a final sample. The scheduler supplies the clock and tick delivery, with `asyncScheduler` as the default.

- This is the sampling policy applied to a periodic notifier. State is independent per operator subscription, and teardown releases both source and timer.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/sampleTime.ts)
- [Related sample implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/sample.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
