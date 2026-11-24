# throttle

```text
Operator:       throttle
Stream shape:   T* → T*
State:          Option<InnerId> — active duration gate
Initial state:  None
Value step:     (S × T) → (S × Action[]) — emit and start duration if idle; otherwise ignore
Ownership:      One duration gate and source subscription per operator subscription
Lifecycle:      Source complete completes immediately; errors forward; cancellation closes source and duration silently
```

**Family:** Duration-controlled selection · **Reference:** RxJS 7.8.2 · **Scope:** Default leading: true, trailing: false; Observable-returning duration selector · **Format:** Compact

> Forward an admitted value, then ignore new values until the selected duration releases the gate.

## Notation and scope

- `None` is an open gate and `Some(id)` identifies a running duration. The admitted value is emitted before its duration selector is called. Ignored values do not invoke that selector.

- Either a duration `next` or its completion releases the gate. In this default policy there is no trailing value to flush, and source completion does not wait for the duration.

- Duration timing is supplied externally; no sharing is introduced. Other leading/trailing configurations have different pending-value and completion rules and are outside this compact scope.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throttle.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
