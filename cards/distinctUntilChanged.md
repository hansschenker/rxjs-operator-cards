# distinctUntilChanged

```text
Operator:       distinctUntilChanged
Stream shape:   T* → T*
State:          Option<K> — key of the last emitted value
Initial state:  None
Value step:     (Option<K> × T) → (Option<K> × Option<T>) — emit first or unequal key
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; selector/comparator errors terminate; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** distinctUntilChanged(comparator?, keySelector?), identity key selector by default · **Format:** Compact

> Compare each arrival with the last emitted key and forward it only when the comparator reports a change.

## Notation and scope

K is the selected key type; with the identity selector K = T. The default comparator is ===. The first value emits without comparison, but its key selector still runs.

When a value is suppressed, the remembered key stays unchanged. Update the key before forwarding a newly accepted value. Option permits actual null or undefined keys.

Timing follows source arrival; no scheduler or sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinctUntilChanged.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
