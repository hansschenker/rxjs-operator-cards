# concatMapTo

```text
Operator:       concatMapTo
Stream shape:   T* → R*
State:          Option<InnerId> × T[] × Bool
Initial state:  (None, [], false)
Value step:     (S × T) → (S × Action[]) — subscribe to inner when idle; otherwise enqueue the trigger
Ownership:      One trigger queue and current inner connection per operator subscription
Lifecycle:      Outer complete drains queued runs; any error closes all; cancellation discards the queue silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `concatMapTo(inner)`; fixed Observable `inner: R*`, no result selector · **Format:** Compact

> Each source value requests another turn of the same inner description, with one turn running at a time.

## Notation and scope

- `S` contains the current inner identity, queued source values, and the outer-completed flag. The values act only as triggers in this no-result-selector scope.
- An accepted trigger subscribes to the fixed `inner`; its values forward. Normal inner completion opens the next queued turn, or completes when the outer is done and nothing remains.
- This deprecated operator delegates to [concatMap](concatMap.md) with a constant projection. Its compatibility import is available from `rxjs/operators`.
- Repeated subscription is not automatic sharing: a cold inner starts a new execution each turn, while an explicitly shared inner retains its own policy. No scheduler is added.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/concatMapTo.ts)
- [Delegated concat-map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/concatMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
