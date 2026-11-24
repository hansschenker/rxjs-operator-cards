# audit

```text
Operator:       audit
Stream shape:   T* → T*
State:          Option<T> × Option<InnerId> × Bool — latest, duration, sourceDone
Initial state:  (None, None, false)
Value step:     (S × T) → (S × Action[]) — replace latest; start duration only if idle
Ownership:      One pending value and duration subscription per operator subscription
Lifecycle:      Source complete may wait for duration; errors discard and error; cancellation closes silently
```

**Family:** Duration-controlled selection · **Reference:** RxJS 7.8.2 · **Scope:** Observable-returning duration selector · **Format:** Compact

> Start a duration on the first arrival, remember the latest arrival during it, and emit that value when the duration nexts.

## Notation and scope

- The state holds the pending value, active duration identity, and whether the source has completed. New values update the candidate without replacing an active duration.

- A duration `next` clears the duration and emits the latest candidate. Duration completion alone clears the duration without flushing; if the source is already complete, it completes the output.

- Source completion waits only when a pending value and active duration remain. Source/duration/selector errors discard pending work. Duration sources determine timing; each operator subscription owns independent state.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/audit.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
