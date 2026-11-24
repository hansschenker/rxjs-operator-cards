# find

```text
Operator:       find
Stream shape:   T* → (T | Undefined)?
State:          Nat — predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — first match emits and completes
Ownership:      One state per operator subscription
Lifecycle:      No match on completion emits undefined then completes; errors forward; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** find(predicate), without deprecated thisArg · **Format:** Compact

> Emit the first matching value, or an actual undefined value if the source completes without one.

## Notation and scope

Undefined names the payload value undefined. It is an emitted value in the no-match case, distinct from no emission. The predicate receives value, index, and source Observable.

After a match, close the source subscription. Source/predicate errors terminate without the no-match fallback. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/find.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
