# auditTime

```text
Operator:       auditTime
Stream shape:   T* → T*
State:          Option<T> × Option<TimerId> × Bool — latest, timer, sourceDone
Initial state:  (None, None, false)
Value step:     (S × T) → (S × Action[]) — replace latest; schedule only if idle
Ownership:      One pending value and timer per operator subscription
Lifecycle:      Source complete waits for pending timer; errors discard and error; cancellation cancels work silently
```

**Family:** Time selection · **Reference:** RxJS 7.8.2 · **Scope:** Positive duration; asynchronous or virtual scheduler · **Format:** Compact

> Emit the latest value at the end of a fixed interval started by the first arrival in that interval.

## Notation and scope

- The state is the latest candidate, active timer, and source-completed flag. Arrivals during the interval replace the candidate without moving the deadline.

- The timer tick emits the candidate. If the source completed with a pending timer, that final timed emission is followed by completion; completion does not flush immediately as `debounceTime` does.

- The scheduler supplies timer timing, defaulting to `asyncScheduler`. Error or cancellation discards the candidate and releases source/timer subscriptions. No sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/auditTime.ts)
- [Related audit implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/audit.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
