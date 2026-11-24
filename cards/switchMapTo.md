# switchMapTo

```text
Operator:       switchMapTo
Stream shape:   T* → R*
State:          Option<InnerId> × Bool
Initial state:  (None, false)
Value step:     (S × T) → (S × Action[]) — close current inner, then subscribe to the fixed inner again
Ownership:      One current inner connection per operator subscription
Lifecycle:      Outer complete waits for current run; any error closes all; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `switchMapTo(inner)`; fixed Observable `inner: R*`, no result selector · **Format:** Compact

> Each source value replaces the current subscription with a fresh subscription to the same inner description.

## Notation and scope

- `S` contains the optional current inner identity and the outer-completed flag. The source payload is a trigger; only current-inner values flow onward.
- Replacement closes the previous connection before the new subscription starts. Current-inner completion clears the slot and completes the result if the outer is already done.
- This deprecated operator delegates to [switchMap](switchMap.md) with a constant projection. Its compatibility import is available from `rxjs/operators`.
- A cold inner restarts its execution; subscribing again to a hot/shared input does not rewind its producer. No scheduler or additional sharing is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchMapTo.ts)
- [Delegated switch-map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
