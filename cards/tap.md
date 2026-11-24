# tap

```text
Operator:       tap
Stream shape:   T* → T*
State:          Bool — source has not completed or errored
Initial state:  true
Value step:     (Bool × T) → (Bool × Action[]) — invoke next hook, then forward the same value
Ownership:      One lifecycle flag per operator subscription; supplied hooks may share external state
Lifecycle:      Forward terminals after hooks; notification-hook failures error; teardown runs applicable lifecycle hooks
```

**Family:** Observation and effects · **Reference:** RxJS 7.8.2 · **Scope:** Observer-object or next-function overload; no deprecated callback tuple · **Format:** Compact

> Observe stream activity through supplied hooks while forwarding each value after its next hook returns.

## Notation and scope

- The initial flag is cleared on the source complete/error paths. The `subscribe` hook runs before source connection; the `next` hook runs before forwarding that value.

- A throwing next/error/complete hook becomes an output error. The `unsubscribe` hook applies to teardown outside the source complete/error paths. The `finalize` hook follows during teardown of the established subscription, provided an applicable `unsubscribe` hook returns; a throwing `unsubscribe` hook prevents that `finalize` call. Teardown-hook exceptions are teardown failures, not replacement output notifications.

- The same payload reference is forwarded, so mutations performed by hooks remain visible. Hook effects are explicit user behavior; this operator adds no scheduler, buffering, or sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/tap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
