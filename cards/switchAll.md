# switchAll

```text
Operator:       switchAll
Stream shape:   (T*)* → T*
State:          Option<InnerId> × Bool
Initial state:  (None, false)
Value step:     (S × T*) → (S × Action[]) — close the previous inner, then connect the replacement
Ownership:      One current inner connection per operator subscription
Lifecycle:      Outer complete waits for current inner; any error closes all; cancellation closes silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `switchAll()`; Observable-valued outer · **Format:** Compact

> Each arriving inner stream replaces the current connection, so only its future values flow onward.

## Notation and scope

- `S` contains the optional current inner identity and the outer-completed flag. Replacement closes the previous connection before opening the new one.
- Current-inner values forward; its completion clears the slot and completes the result if the outer has already completed.
- Closing a connection prevents its later delivery through this operator; it does not retract prior outputs or promise to undo external work.
- This is `switchMap(identity)`. There is no queue, scheduler, or added sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchAll.ts)
- [Switch policy](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/switchMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
