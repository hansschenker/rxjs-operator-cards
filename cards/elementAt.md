# elementAt

```text
Operator:       elementAt
Stream shape:   T* → T?
State:          Nat — zero-based source index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — emit at requested index, then complete
Ownership:      One state per operator subscription
Lifecycle:      Match completes early; short source errors on completion; source error forwards; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** elementAt(index), with a nonnegative integer index and no default · **Format:** Compact

> Select the source value at one zero-based position and finish immediately.

## Notation and scope

The requested index is fixed; the state counts arriving source values. Values before that position are ignored. A negative index throws ArgumentOutOfRangeError when the operator is constructed.

If completion arrives before the requested value, send an ArgumentOutOfRangeError error notification. A second default-value argument changes that case to a default emission then completion. Timing follows the source; no scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/elementAt.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 take implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/take.ts)
- [RxJS 7.8.2 throwIfEmpty implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
