# retry

```text
Operator:       retry
Stream shape:   T* → T*
State:          Nat — retries used
Initial state:  0
Value step:     (Nat × T) → (Nat × T) — forward value without resetting count
Ownership:      One retry counter and current source subscription per operator subscription
Lifecycle:      Error resubscribes while budget remains; final error forwards; source completion completes; cancellation stops retries
```

**Family:** Error resubscription · **Reference:** RxJS 7.8.2 · **Scope:** Nonnegative integer count n, or default Infinity; no delay/resetOnSuccess config · **Format:** Compact

> Reconnect to the same source after failure while the permitted retry budget remains.

## Notation and scope

- `n` counts additional subscriptions after failures, so `retry(0)` still performs the original source subscription. Values from failed attempts remain delivered and may be repeated by later attempts.

- The count does not reset on successful values in this scope. Source completion stops retrying; exhausting the budget forwards the last error.

- Immediate retries introduce no scheduler or sharing. Configured delays and `resetOnSuccess` require extra policy/state: a delay notifier next retries, empty completion completes, and error terminates.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/retry.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
