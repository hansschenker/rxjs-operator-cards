# timeoutWith

```text
Operator:       timeoutWith
Stream shape:   T* → (T | R)*
State:          Source(Option<TimerId>) | Fallback
Initial state:  Source(None)
Value step:     (S × T) → (S × Action[]) — in Source, cancel timer, emit value, and arm the next deadline
Ownership:      One timer and source-or-fallback connection per operator subscription
Lifecycle:      Deadline closes source and opens fallback; active input terminals forward; cancellation closes work silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `timeoutWith(d, fallback, scheduler?)`; finite `d > 0`, Observable fallback `R*` · **Format:** Compact

> If the source leaves a gap longer than the configured wait, switch delivery permanently to the fallback stream.

## Notation and scope

- `Source(timer)` records the optional owned deadline; `Fallback` means the replacement is connected. `S` omits timeout diagnostic fields unused by this fixed-fallback form.
- The timer uses the supplied scheduler, or `asyncScheduler`. Setup starts the first deadline if no synchronous value arrived; each source next cancels the old timer and schedules a new `d`-length wait.
- On deadline, unsubscribe the original source before subscribing to the fixed fallback. Fallback values forward directly and are not monitored by another timeout in this operator.
- This deprecated form delegates to [timeout](timeout.md) with a replacement factory. Its compatibility import is available from `rxjs/operators`.
- The `Date` overload monitors only the first value against an absolute scheduler-clock deadline; it and nonpositive durations are outside this numeric-gap model. No sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/timeoutWith.ts)
- [Timeout implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/timeout.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
