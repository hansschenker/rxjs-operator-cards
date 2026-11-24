# bufferTime

```text
Operator:       bufferTime
Stream shape:   T* → (T[])*
State:          T[] × TimerId — current batch and closing task
Initial state:  (Empty batch, first scheduled closing task)
Value step:     (S × T) → (S × Action[]) — append; no immediate output
Ownership:      One batch and scheduler-owned closing task per operator subscription
Lifecycle:      Completion flushes then completes; error/cancel discards the batch and releases scheduled work
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** bufferTime(span, scheduler?), positive span; no creation interval or size limit · **Format:** Compact

> Publish the collected array at each scheduled boundary, then begin the next batch.

## Notation and scope

TimerId identifies scheduled closing work. Setup starts an empty batch and schedules its close; each closing event publishes the batch, even if empty, and starts another.

Normal completion publishes the current batch, even if empty, before completing. Error forwards without flushing. Cancellation closes source and scheduled work silently.

The scheduler (asyncScheduler by default) supplies the clock and delivery mechanism. Separate creation intervals or maxBufferSize change the policy and are outside this scope; no sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/bufferTime.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
