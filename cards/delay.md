# delay

```text
Operator:       delay
Stream shape:   T* → T*
State:          Pending<T>[] × Bool — delayed values and sourceDone
Initial state:  ([], false)
Value step:     (S × T) → (S × Action[]) — create a timer for this value
Ownership:      One set of pending value/timer subscriptions per operator subscription
Lifecycle:      Source complete waits for pending values; errors discard and error; cancellation cancels pending work
```

**Family:** Time delivery · **Reference:** RxJS 7.8.2 · **Scope:** Positive numeric delay; asynchronous or virtual scheduler · **Format:** Compact

> Retain every arriving value until its own scheduler-controlled delay expires.

## Notation and scope

- `Pending<T>` pairs a retained value with its timer identity. A timer emits its value once and then removes that pending entry; source completion waits until the entries finish.

- A source error is forwarded immediately and cancels pending values. An empty source completes immediately because completion is not independently delayed.

- Timing comes from the supplied scheduler, defaulting to `asyncScheduler`; the operator does not share execution. The Date overload uses a common absolute target and is outside this numeric-delay scope.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/delay.ts)
- [Related delayWhen implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/delayWhen.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
