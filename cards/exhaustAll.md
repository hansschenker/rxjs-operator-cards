# exhaustAll

```text
Operator:       exhaustAll
Stream shape:   (T*)* → T*
State:          Option<InnerId> × Bool
Initial state:  (None, false)
Value step:     (S × T*) → (S × Action[]) — connect only when idle; ignore arrivals while busy
Ownership:      One accepted inner connection per operator subscription
Lifecycle:      Outer complete waits for accepted inner; any error closes all; cancellation closes silently
```

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `exhaustAll()`; Observable-valued outer · **Format:** Compact

> An accepted inner stream keeps its turn until completion, and intervening arrivals are discarded.

## Notation and scope

- `S` contains the current inner identity and the outer-completed flag. Ignored inner descriptions are neither queued nor subscribed.
- Accepted-inner values forward. Its completion frees the slot; if the outer is done, the result completes.
- An inner that never completes keeps the gate closed. Only a later outer next after the gate reopens can start another inner.
- This is `exhaustMap(identity)`. It introduces no scheduler or sharing; cancellation closes the outer and accepted inner.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/exhaustAll.ts)
- [Exhaust policy](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/exhaustMap.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
