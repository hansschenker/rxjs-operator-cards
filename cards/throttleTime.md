# throttleTime

```text
Operator:       throttleTime
Stream shape:   T* → T*
State:          Option<TimerId> — active throttle timer
Initial state:  None
Value step:     (S × T) → (S × Action[]) — emit and schedule if idle; otherwise ignore
Ownership:      One timer and source subscription per operator subscription
Lifecycle:      Source complete completes immediately; errors forward; cancellation cancels timer and closes silently
```

**Family:** Time selection · **Reference:** RxJS 7.8.2 · **Scope:** Positive duration; asynchronous or virtual scheduler; default leading: true, trailing: false · **Format:** Compact

> Emit the first eligible value and suppress arrivals during the following scheduler-controlled interval.

## Notation and scope

- A running timer closes the gate; its first tick reopens it. The duration starts after an admitted value, rather than on every ignored arrival.

- The scheduler supplies the clock and delivery mechanism; RxJS uses `asyncScheduler` by default. Source completion does not flush ignored values or wait for the timer in this policy.

- No sharing is introduced. This is `throttle` with a timer duration; configurations enabling trailing delivery require additional pending-value state.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throttleTime.ts)
- [Related throttle implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throttle.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
