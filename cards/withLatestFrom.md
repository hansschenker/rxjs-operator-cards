# withLatestFrom

```text
Operator:       withLatestFrom
Stream shape:   A* → (A × B)*
State:          Option<B>
Initial state:  None
Value step:     (Option<B> × A) → (Option<B> × Option<A × B>)
Ownership:      One auxiliary cache and two connections per operator subscription
Lifecycle:      Source completion ends result; any input error closes both; cancellation closes both silently
```

**Family:** Combination · **Reference:** RxJS 7.8.2 · **Scope:** `withLatestFrom(other)`; one Observable `B*`, no projection · **Format:** Compact

> A source value produces a pair with the latest auxiliary value, once an auxiliary value exists.

## Notation and scope

- The fixed `other: B*` is subscribed before the main source. Its next replaces the cache and emits nothing; only a main-source next runs the Value step.
- With `None`, drop that source value. With `Some(b)`, emit `(a, b)` while retaining `b` for later source values.
- Auxiliary completion leaves the cache intact. If it completed empty, source values keep producing no pairs; that auxiliary completion does not itself end the result.
- More auxiliaries require every cache to be populated. Projection overloads are omitted; there is no added scheduler or sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/withLatestFrom.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
