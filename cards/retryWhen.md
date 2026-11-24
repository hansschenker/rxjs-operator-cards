# retryWhen

```text
Operator:       retryWhen
Stream shape:   T* → T*
State:          Run × Notifier × Bool
Initial state:  (Subscribing, NotCreated, false)
Value step:     (S × T) → (S × T) — forward a value from the current source run
Ownership:      One notifier and source-resubscription state per downstream subscription
Lifecycle:      Source completion completes; notifier completion completes immediately; notifier error fails; cancellation closes all silently
```

**Family:** Resubscription compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `retryWhen(notifier)`; one resume signal per error, no independent notifier signals · **Format:** Compact

> After a source run errors, the notifier decides whether and when another source subscription begins.

## Notation and scope

- `Run = Idle | Subscribing | Active(InnerId)`; `Notifier = NotCreated | Active(InnerId)`. The boolean records a pending synchronous resubscription.
- On the first source error, create and subscribe the notifier once. Send each caught source error as a value to its errors stream; notifier next requests another subscription without re-emitting that error downstream.
- Notifier completion immediately completes the result and closes any active source run. Notifier error or a thrown notifier factory errors the result; ordinary source completion ends it successfully.
- If no source handle has yet been assigned, synchronous resume waits until `source.subscribe` returns and that returned subscription is torn down. With an existing handle, a notifier next can start new source setup immediately, before the old terminal handler finishes teardown.
- The one-run state assumes one error-responsive resume at a time. Arbitrary independent or extra notifier emissions can start overlapping source subscriptions in 7.8.2 and are outside this scoped model.
- This deprecated API is available from `rxjs/operators`; [retry](retry.md) with a `delay` option is the modern restart API. No delay is built in, and delivered values are not retracted. A cold source executes again; a shared source keeps its own lifetime policy.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/retryWhen.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
