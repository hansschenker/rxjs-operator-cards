# endWith

```text
Operator:       endWith
Stream shape:   T* → (T | P)*
State:          Source | Suffix(Nat) — forwarding or suffix cursor
Initial state:  Source
Value step:     (Source × T) → (Source × T)
Ownership:      One forwarding/suffix progression per operator subscription; suffix objects retain their references
Lifecycle:      Source complete emits suffix then completes; errors skip suffix; cancellation stops delivery silently
```

**Family:** Completion boundary · **Reference:** RxJS 7.8.2 · **Scope:** Ordinary variadic suffix values; no scheduler overload · **Format:** Compact

> Forward source values and append supplied values only after normal source completion.

## Notation and scope

- `P` is the suffix payload type. `Suffix(i)` records the next suffix argument to deliver; the source-value step leaves the forwarding phase unchanged.

- Normal source completion switches to suffix delivery in argument order. Empty sources still receive the suffix; source error or cancellation prevents it.

- Suffix delivery is synchronous in this scope; source values follow source timing. No sharing is introduced. Suffix argument expressions are evaluated when constructing the operator, not afresh per subscription.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/endWith.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
