# exhaust

```text
Operator:       exhaust
Stream shape:   (T*)* → T*
State:          Option<InnerId> × Bool
Initial state:  (None, false)
Value step:     (S × T*) → (S × Action[]) — connect only when idle; ignore arrivals while busy
Ownership:      One accepted inner connection per operator subscription
Lifecycle:      Outer complete waits for accepted inner; any error closes all; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `exhaust()`; Observable-valued outer · **Format:** Compact

> The accepted inner stream keeps its turn until completion, while intervening arrivals are ignored.

## Notation and scope

- `exhaust` is the deprecated alias of [exhaustAll](exhaustAll.md), available from `rxjs/operators` and `rxjs`.
- `S` contains the optional current inner identity and the outer-completed flag. Ignored inner descriptions are not subscribed or queued.
- Accepted-inner next values forward. Its completion frees the slot, or completes the result if the outer is done; a never-ending accepted inner keeps the gate closed.
- No scheduler or sharing is added. Cancellation closes the outer and accepted inner without a downstream terminal notification.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/exhaust.ts)
- [Delegated exhaust-all implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/exhaustAll.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
