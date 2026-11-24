# debounce

```text
Operator:       debounce
Stream shape:   T* → T*
State:          Option<T> × Option<InnerId> — pending value and duration
Initial state:  (None, None)
Value step:     (S × T) → (S × Action[]) — replace pending value and duration
Ownership:      One pending value and duration subscription per operator subscription
Lifecycle:      Source complete flushes; errors discard and error; cancellation discards and closes silently
```

**Family:** Duration-controlled selection · **Reference:** RxJS 7.8.2 · **Scope:** Observable-returning duration selector · **Format:** Compact

> Keep the latest source value until its selected duration emits, replacing earlier candidates when new values arrive.

## Notation and scope

- `S` pairs the pending value with the current duration subscription identity. Each source value closes the preceding duration, stores the new value, and invokes the duration selector.

- The duration's first `next` releases the pending value and closes that duration. Duration completion alone does not release it. Source completion releases a pending value immediately and then completes.

- Source, duration, or selector errors terminate without flushing. Cancellation closes source and duration subscriptions. Timing comes from those sources; this operator introduces no sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/debounce.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
