# timeout

```text
Operator:       timeout
Stream shape:   T* → T*
State:          Option<T> × Nat × Option<TimerId> — last, seen, deadline
Initial state:  (None, 0, None); first deadline armed during connection setup
Value step:     (S × T) → (S × Action[]) — cancel deadline, forward value, restart deadline
Ownership:      One source subscription and current deadline per operator subscription
Lifecycle:      Deadline closes source and errors; source terminals forward; cancellation cancels deadline silently
```

**Family:** Time boundary · **Reference:** RxJS 7.8.2 · **Scope:** Positive numeric timeout with default TimeoutError; asynchronous or virtual scheduler · **Format:** Compact

> Require a first value and each following value to arrive within the permitted interval.

## Notation and scope

- The state records the last value, received-value count, and current timer. The numeric argument supplies the interval both before the first value and between subsequent values.

- Each source value cancels the prior timer, updates the count/value, forwards the value, and schedules the next deadline. Source completion/error releases the timer; a deadline first closes the source and then emits `TimeoutError`.

- The scheduler supplies time, defaulting to `asyncScheduler`; no sharing is introduced. Date/first-only and custom fallback configurations change the deadline or recovery policy and are outside this scope.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/timeout.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
