# skip

```text
Operator:       skip
Stream shape:   T* → T*
State:          Nat — zero-based source index
Initial state:  0
Value step:     (Nat × T) → (Nat × Option<T>) — admit when index ≥ n
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; cancellation closes upstream silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skip(n), with a nonnegative integer n · **Format:** Compact

> Discard the first n source values, then forward every later arrival.

## Notation and scope

Count n is fixed for the operator. The index advances on every source value, including skipped values; n = 0 forwards everything.

Timing follows the source; no timer, notifier, or sharing is introduced. An early source completion simply ends the result, even if fewer than n values arrived.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skip.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
