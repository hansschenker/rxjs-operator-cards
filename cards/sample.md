# sample

```text
Operator:       sample
Stream shape:   T* → T*
State:          Option<T> — latest value since the previous sample
Initial state:  None
Value step:     (Option<T> × T) → (Option<T> × Action[]) — remember; emit nothing yet
Ownership:      One pending value and source/notifier pair per operator subscription
Lifecycle:      Source complete discards pending and completes; either error forwards; cancellation closes both silently
```

**Family:** Notifier-controlled sampling · **Reference:** RxJS 7.8.2 · **Scope:** Observable notifier · **Format:** Compact

> On each notifier value, publish the most recent source value received since the preceding sample.

## Notation and scope

- Source arrivals replace the pending value. When a notifier `next` finds a pending value, it clears the pending state before emitting the saved value, so a second tick without new source data emits nothing.

- The source is subscribed before the notifier. Notifier completion alone neither flushes nor completes the output. Source completion stops sampling without a final flush.

- The notifier supplies sampling times; the operator adds no clock or sharing. Errors from either connection terminate and release both; cancellation also discards pending state.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/sample.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
