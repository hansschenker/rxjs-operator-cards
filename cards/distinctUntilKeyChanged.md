# distinctUntilKeyChanged

```text
Operator:       distinctUntilKeyChanged
Stream shape:   T* → T*
State:          Option<T> — last emitted object reference
Initial state:  None
Value step:     (Option<T> × T) → (Option<T> × Option<T>) — compare the selected properties
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; property/comparator failures terminate; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** distinctUntilKeyChanged(key, compare?), with key in T · **Format:** Compact

> Forward the first object and subsequent objects whose selected property differs from the last emitted object’s property.

## Notation and scope

The default property comparison is ===. A supplied compare function receives the two property values. The first object is emitted without comparing properties.

The implementation retains the full last emitted object, not a snapshot of its key value: it reads previous[key] and current[key] on each comparison. Mutation of a retained object can therefore change a later comparison.

No cloning, scheduling, or sharing is introduced. Values that compare equal leave the remembered object unchanged.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinctUntilKeyChanged.ts)
- [RxJS 7.8.2 distinctUntilChanged implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinctUntilChanged.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
