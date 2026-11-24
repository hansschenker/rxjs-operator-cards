# delayWhen

```text
Operator:       delayWhen
Stream shape:   T* → T*
State:          Pending<T>[] × Nat × Bool — durations, selector index, sourceDone
Initial state:  ([], 0, false)
Value step:     (S × T) → (S × Action[]) — retain value and connect its selected duration
Ownership:      One pending entry per accepted source value, owned by its operator subscription
Lifecycle:      Source complete waits for durations; errors cancel and error; cancellation discards pending values
```

**Family:** Duration-controlled delivery · **Reference:** RxJS 7.8.2 · **Scope:** Observable-returning selector; no deprecated subscriptionDelay argument · **Format:** Compact

> Give each value its own duration stream and release that value on the duration's first emission.

## Notation and scope

- `Pending<T>` stores the value and its duration subscription identity; `Nat` is the next selector index. Each selected duration is independent, so released values can arrive in a different order from the source.

- In RxJS 7.8.2, a duration must produce `next` to release its value. Completing without a value drops that pending source value. Source completion waits for all active duration subscriptions to finish.

- Selector/source/duration errors terminate and close all owned work. The selected streams supply timing; no scheduler or sharing is imposed. The deprecated subscription-delay overload has additional setup rules.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/delayWhen.ts)
- [Related mergeMap implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
