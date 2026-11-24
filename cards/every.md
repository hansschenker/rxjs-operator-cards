# every

```text
Operator:       every
Stream shape:   T* → Bool?
State:          Nat — next predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — continue if true; emit false and complete if false
Ownership:      One predicate index and source subscription per operator subscription
Lifecycle:      All-true normal completion emits true; errors emit no answer; early false closes source; cancellation is silent
```

**Family:** Quantification · **Reference:** RxJS 7.8.2 · **Scope:** Predicate overload without deprecated thisArg · **Format:** Compact

> Decide whether every received value satisfies a supplied predicate, stopping as soon as one does not.

## Notation and scope

- The predicate receives the value, zero-based source index, and source Observable. A false result emits `false` and completes immediately; a true result produces no output yet.

- Normal completion emits `true`, including empty-source completion. `Bool?` is an upper bound: an error, cancellation, or endless sequence of accepted values can prevent any result.

- Predicate failures become output errors. No scheduler or sharing is introduced; the answer is delivered during the decisive source notification.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/every.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
