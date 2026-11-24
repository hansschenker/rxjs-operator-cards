# repeatWhen

```text
Operator:       repeatWhen
Stream shape:   T* → T*
State:          Run × Notifier × Bool × Bool
Initial state:  (Subscribing, NotCreated, false, false)
Value step:     (S × T) → (S × T) — forward a value from the current source run
Ownership:      One notifier and source-resubscription state per downstream subscription
Lifecycle:      Source error fails; complete when source and notifier are done; notifier error fails; cancellation closes all silently
```

**Family:** Resubscription compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `repeatWhen(notifier)`; one resume signal per completion, no independent notifier signals · **Format:** Compact

> After a source run completes, the notifier decides when another subscription to the source begins.

## Notation and scope

- `Run = Idle | Subscribing | Active(InnerId)`; `Notifier = NotCreated | Active(InnerId) | Done`. The booleans record source-completed and pending synchronous resubscription.
- The notifier is created and subscribed only on the first source completion, then reused. Each source completion sends a `void` value into its notification stream; a notifier next requests resubscription.
- Notifier completion does not interrupt an active source run: result completion requires the source-completed flag as well. A waiting completed source ends immediately when the notifier completes.
- If no source handle has yet been assigned, synchronous resume waits until `source.subscribe` returns and that returned subscription is torn down. With an existing handle, a notifier next can start new source setup immediately, before the old terminal handler finishes teardown.
- The one-run state explicitly assumes one completion-responsive resume at a time. Arbitrary independent or extra notifier emissions can start overlapping source subscriptions in 7.8.2 and are outside this scoped model.
- Source errors and notifier construction/errors fail the result. This deprecated API is available from `rxjs/operators`; [repeat](repeat.md) with a `delay` option is the modern restart API. No delay is added here: notifier/source timing controls restart, and shared sources retain their own policy.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/repeatWhen.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
