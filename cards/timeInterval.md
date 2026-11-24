# timeInterval

```text
Operator:       timeInterval
Stream shape:   T* → Interval<T>*
State:          Time — previous clock reading
Initial state:  scheduler.now() at subscription
Value step:     (Time × T × Time) → (Time × Interval<T>)
Ownership:      One previous-time slot per operator subscription
Lifecycle:      Forward source terminals; clock-read errors terminate; cancellation closes upstream silently
```

**Family:** Time measurement · **Reference:** RxJS 7.8.2 · **Scope:** Default or supplied SchedulerLike used as a clock · **Format:** Compact

> Attach the elapsed clock interval since subscription or the preceding value to each arrival.

## Notation and scope

- `Interval<T>` is `{ value: T, interval: number }`. For previous time `p` and current clock input `now`, the next state is `now` and the output interval is `now - p`.

- The first interval is measured from this operator subscription, not from an earlier event. The default clock is `asyncScheduler`; reading it does not introduce scheduling or delayed delivery.

- Independent subscriptions remember independent previous readings. The supplied clock defines units and monotonicity; the payload is preserved and no sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/timeInterval.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
