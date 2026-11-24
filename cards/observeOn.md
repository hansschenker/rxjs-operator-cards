# observeOn

```text
Operator:       observeOn
Stream shape:   T* → T*
State:          Pending<T>[] — scheduled notification deliveries
Initial state:  []
Value step:     (S × T) → (S × Action[]) — schedule a Next notification
Ownership:      Each operator subscription owns its queued delivery tasks and source connection
Lifecycle:      Complete/error are scheduled too; cancellation cancels pending deliveries and closes upstream
```

**Family:** Delivery scheduling · **Reference:** RxJS 7.8.2 · **Scope:** SchedulerLike and nonnegative delay · **Format:** Compact

> Schedule delivery of every source notification using the selected execution mechanism.

## Notation and scope

- `Pending<T>` pairs a notification (`Next(T)`, `Error`, or `Complete`) with its scheduled task. A source value schedules a delivery instead of forwarding directly.

- Source completion and error are also scheduled with the chosen delay; a source error is not treated like the immediate-error policy of `delay`. The scheduler determines delivery order and execution context.

- Source subscription itself occurs normally. Closing the output cancels queued deliveries; ordinary source timing and any existing sharing remain properties of the source.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/observeOn.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
