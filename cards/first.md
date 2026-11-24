# first

```text
Operator:       first
Stream shape:   T* → T?
State:          Nat — predicate index while searching
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — first match emits and completes
Ownership:      One state per operator subscription
Lifecycle:      First match closes upstream; empty matching result errors on completion; errors/cancel end the search
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** first(predicate), without a default value · **Format:** Compact

> Emit the first value accepted by the predicate and immediately finish.

## Notation and scope

The predicate receives value, zero-based source index, and the source Observable. Omitting it accepts the first source value. A thrown predicate error is forwarded.

If the source completes without a match, send EmptyError. Supplying a second default-value argument changes that case to one default emission followed by completion, including when the supplied default is undefined.

T? bounds next notifications; it does not promise a result before error or cancellation. Cancellation closes upstream silently. No scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/first.ts)
- [RxJS 7.8.2 take implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/take.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 throwIfEmpty implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
