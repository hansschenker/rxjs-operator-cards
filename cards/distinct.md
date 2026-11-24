# distinct

```text
Operator:       distinct
Stream shape:   T* → T*
State:          Set<K> — keys already emitted
Initial state:  Empty set
Value step:     (Set<K> × T) → (Set<K> × Option<T>) — emit only a previously unseen key
Ownership:      One key set and optional flush subscription per operator subscription
Lifecycle:      Source completion/error terminates; flush next clears keys; flush error forwards; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** distinct(keySelector?, flushes?), Observable flush notifier when supplied · **Format:** Compact

> Emit the first value for each key until a flush event clears the remembered keys.

## Notation and scope

K is selected from each value, or is T itself when no selector is supplied. Set membership uses JavaScript Set equality (SameValueZero), rather than distinctUntilChanged’s default === comparison.

A flush next clears the set without emitting. Flush completion alone does nothing; source subscription is established before the optional flush subscription. Selector errors terminate with error.

The set can grow without bound between flushes. Timing comes from source and flush notifications; there is no scheduler or introduced sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinct.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
