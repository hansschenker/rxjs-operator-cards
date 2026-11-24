# skipUntil

```text
Operator:       skipUntil
Stream shape:   T* → T*
State:          Bool — forwarding gate
Initial state:  false
Value step:     (Bool × T) → (Bool × Option<T>) — forward only after the gate opens
Ownership:      One gate and source/notifier subscription pair per operator subscription
Lifecycle:      Notifier next opens gate; source complete completes; either error forwards; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skipUntil(notifier), with an Observable notifier · **Format:** Detailed

> Ignore source values until the notifier sends a value, then forward subsequent source values.

## Type shape

```text
skipUntil :: N* → T* → T*
```

The supplied `N*` is a notifier; its payload has no significance to the gate. Only its first `next` matters. Output values keep their original type and order. This signature does not promise any value or eventual completion; other `ObservableInput` notifier forms are outside this card's scope.

## Intuition

A screen receives readings immediately, but should display them only after a separate ready signal. Readings arriving before readiness are discarded. The first ready value opens the gate permanently; future readings pass through immediately. Nothing buffers the earlier readings for later delivery, and readiness itself is never displayed as a reading.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | With a closed gate, emit nothing; with an open gate, forward the value unchanged. |
| Whole connection | Produce the source suffix observed after the first notifier value, until source termination or downstream closure. |

The gate never closes again. If the notifier completes without producing a value, every source value remains suppressed. The source can nevertheless complete normally, producing an empty result. A silent, nonterminating source does not finish merely because its gate never opens.

## Remembers

`Bool` is the forwarding gate, initially `false`. Each operator subscription owns an independent gate and source/notifier subscriptions. In the header's local result, `None` means no emitted value; it does not mean emitting `undefined`. The first notifier value closes that notifier subscription and sets the gate to `true`. No source payload is retained.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize the gate; subscribe to the notifier before subscribing to the source. |
| Source next | Forward only when the gate is open. |
| First notifier next | Close the notifier subscription and open the gate; emit no notifier payload. |
| Notifier complete | Leave the gate unchanged; release only the notifier connection. |
| Notifier error | Forward the error and close owned connections. |
| Source complete | Complete downstream, even with a closed gate; release the notifier. |
| Source error | Forward the error regardless of gate state; release the notifier. |
| Downstream close | Release both connections without emitting completion. |

## Time, cancellation, and sharing

- **Time:** No scheduler is introduced. Source and notifier delivery order determines admission. A synchronous notifier value opens the gate before a synchronous source starts delivering.
- **Cancellation:** The notifier subscription ends on its first value. Source termination or downstream closure releases remaining connections. Closing this subscription does not undo effects or necessarily stop an independently shared producer.
- **Sharing:** No sharing or replay is introduced. A live notifier's earlier values are missed by later connections. Observers of the same shared source can therefore have different gate states.

## Worked trace

Illustrative cold source and cold notifier connect at `t = 0`; times are virtual milliseconds relative to connection. Source plan: `a@10`, `b@20`, `c@40`, complete at `50`. The notifier sends `ready@30`.

| Time | Event | Gate and output |
| --- | --- | --- |
| 0 | Connect notifier, then source | Closed; no output |
| 10 | Source `a` | Discard |
| 20 | Source `b` | Discard |
| 30 | Notifier `ready` | Open; notifier disconnects; no output |
| 40 | Source `c` | Emit `c` |
| 50 | Source complete | Complete downstream |

## Verification obligations

- Reproduce the trace, including notifier teardown at `30`.
- Check synchronous opening and notifier-before-source connection order.
- Confirm notifier completion alone never opens the gate.
- Verify both error origins and silent downstream cancellation release connections.
- Check that late connections to a live notifier do not inherit another connection's open gate.

## Common confusion

Waiting for the notifier to **complete** will not open this gate; it must emit a value. Nor does a closed gate delay source subscription or prevent its work.

One 7.8.2 setup detail matters: after a **synchronous notifier error**, `skipUntil` still attempts source subscription. A custom source initializer can run with an already closed subscriber; no values reach downstream. Unlike `takeUntil`, this implementation has no closed-state guard around that subscription attempt.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/skipUntil)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skipUntil.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
