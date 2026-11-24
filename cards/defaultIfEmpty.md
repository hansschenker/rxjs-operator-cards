# defaultIfEmpty

```text
Operator:       defaultIfEmpty
Stream shape:   T* → (T | D)*
State:          Bool — has received a source value
Initial state:  false
Value step:     (Bool × T) → (Bool × T) — mark nonempty and forward
Ownership:      One nonempty flag per operator subscription; default payload is retained by reference
Lifecycle:      Empty completion emits default then completes; other terminals forward; cancellation is silent
```

**Family:** Completion default · **Reference:** RxJS 7.8.2 · **Scope:** Explicit default value D · **Format:** Compact

> Supply one default value only if the source reaches normal completion without emitting a value.

## Notation and scope

- The boolean becomes true on the first source value and remains true. Every source value is forwarded unchanged.

- Only normal empty completion invokes the default-delivery rule. Error and cancellation never substitute the default, and an endlessly silent source does not trigger it.

- There is no clock or sharing. A default array or function remains one payload; the operator neither clones the object nor invokes a function default.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/defaultIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
