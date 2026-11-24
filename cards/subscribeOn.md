# subscribeOn

```text
Operator:       subscribeOn
Stream shape:   T* → T*
State:          Waiting | Active — source connection phase
Initial state:  Waiting; schedule source subscription
Value step:     (Active × T) → (Active × T)
Ownership:      One setup task and eventual source subscription per operator subscription
Lifecycle:      Cancellation before setup prevents connection; afterward source terminals forward and closure releases upstream
```

**Family:** Subscription scheduling · **Reference:** RxJS 7.8.2 · **Scope:** Asynchronous or virtual scheduler and nonnegative delay · **Format:** Compact

> Schedule when the source subscription begins, then let its notifications flow normally.

## Notation and scope

- `Waiting` means source setup has been scheduled; `Active` means that task has subscribed to the source. The value step applies only in the active phase.

- The scheduler controls subscription timing. It does not independently reschedule each subsequent notification; `observeOn` supplies that separate policy.

- Canceling a pending setup task prevents source subscription. After setup, output closure closes the source subscription. This operator introduces no sharing, and a live source can emit before setup occurs.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/subscribeOn.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
