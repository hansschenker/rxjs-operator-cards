# throwIfEmpty

```text
Operator:       throwIfEmpty
Stream shape:   T* → T*
State:          Bool — has received a source value
Initial state:  false
Value step:     (Bool × T) → (Bool × T) — mark nonempty and forward
Ownership:      One nonempty flag per operator subscription
Lifecycle:      Empty completion errors; nonempty completion completes; source errors forward; cancellation is silent
```

**Family:** Completion requirement · **Reference:** RxJS 7.8.2 · **Scope:** Optional error factory; default EmptyError · **Format:** Compact

> Require at least one value before normal completion, failing if the source completes empty.

## Notation and scope

- Each source value sets the flag and passes through. On completion, a false flag invokes the error factory instead of completing normally.

- The default factory creates `EmptyError`. A source error is forwarded directly, and cancellation does not call the empty-input error factory.

- An indefinitely silent source does not count as completed empty input. No scheduler, buffering, or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
