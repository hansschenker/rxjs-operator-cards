# takeWhile

```text
Operator:       takeWhile
Stream shape:   T* → T*
State:          Nat — predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — emit if predicate succeeds; otherwise complete
Ownership:      One state per operator subscription
Lifecycle:      First rejection completes and closes upstream; errors forward; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** takeWhile(predicate), inclusive = false · **Format:** Compact

> Forward the initial run of values accepted by the predicate, then end at the first rejection.

## Notation and scope

The predicate receives the source value and zero-based index. A rejected value is omitted in this scope, and later values are not inspected. A thrown predicate error terminates with error.

The inclusive = true variant additionally emits the first rejected value before completing. Source completion/error is otherwise forwarded. No scheduling or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeWhile.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
