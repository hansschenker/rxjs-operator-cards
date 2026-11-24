# findIndex

```text
Operator:       findIndex
Stream shape:   T* → (Nat | -1)?
State:          Nat — predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — first match emits its index and completes
Ownership:      One state per operator subscription
Lifecycle:      No match on completion emits -1 then completes; errors forward; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** findIndex(predicate), without deprecated thisArg · **Format:** Compact

> Publish the index of the first matching source value, or -1 after a completed search with no match.

## Notation and scope

Nat is the zero-based index, and -1 is the no-match result. The predicate receives value, index, and source Observable.

Matching closes upstream immediately after the index is delivered. Source/predicate errors do not produce -1. Timing follows the source; no scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/findIndex.ts)
- [RxJS 7.8.2 find implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/find.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
