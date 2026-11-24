# takeUntil

```text
Operator:       takeUntil
Stream shape:   T* → T*
State:          Unit — no payload memory
Initial state:  ()
Value step:     (Unit × T) → (Unit × T) — forward while open
Ownership:      One source/notifier subscription pair per operator subscription
Lifecycle:      Notifier next/source complete completes; either error forwards; cancellation closes both silently
```

**Family:** Notifier selection · **Reference:** RxJS 7.8.2 · **Scope:** Observable source and Observable notifier

> Forward arriving source values until the notifier produces its first value, then complete and release both connections.

## Type shape

```text
takeUntil :: N* → T* → T*
```

`N*` is the supplied notifier. Any `next` is the stopping signal, regardless of payload. Other `ObservableInput` forms are omitted. Output cardinality can be zero, finite, or unbounded.

## Intuition

A screen displays incoming readings. Closing it produces a value on a separate stop stream. Readings pass through unchanged until that value arrives, then the displayed stream completes. The stopping signal never becomes displayed data.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | While connected, forward the value unchanged. |
| Whole connection | Produce the prefix observed before the notifier's first `next`, unless the source terminates or downstream closes earlier. |

A silent source and silent notifier can leave the connection open indefinitely.

## Remembers

Each connection owns a notifier subscription and, if admitted, a source subscription. There is no buffer or counter. The downstream subscription's open or closed state determines whether forwarding remains possible.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Subscribe to the notifier first. Subscribe to the source only if downstream is still open afterward. |
| Source next | Forward the payload unchanged. |
| Notifier next | Complete downstream and release both subscriptions. The notifier payload is not emitted. |
| Notifier complete | Stop observing that notifier; continue with the source. Completion alone is not a stopping signal. |
| Notifier error | Error downstream and release owned subscriptions. |
| Source complete | Complete downstream and release the notifier subscription. |
| Source error | Error downstream and release the notifier subscription. |
| Downstream close | Release both subscriptions without sending completion. |

## Time, cancellation, and sharing

- **Time:** The source and notifier determine delivery order; no timer or scheduler is added. A notifier that synchronously emits during connection prevents source subscription entirely. A synchronous notifier error also prevents it.
- **Cancellation:** The operator closes its subscriptions. Physical work stops only when those subscriptions' teardown and ownership permit it; completed effects are not reversed.
- **Sharing:** No sharing is introduced. A live, non-replaying notifier does not remember stop values emitted before this connection existed. An existing shared source keeps its own lifetime policy.

## Worked trace

Illustrative cold source and cold notifier connect at `t = 0`. Times are milliseconds relative to connection. The source plans `a@10`, `b@20`, `c@40`, complete at `50`; the notifier emits `stop@30`.

| Time | Event | Output and connection state |
| --- | --- | --- |
| 0 | Connect notifier, then source | Open; no output |
| 10 | Source `a` | `a` |
| 20 | Source `b` | `b` |
| 30 | Notifier `stop` | Complete; both subscriptions close |
| 40, 50 | Remaining source work | Cancelled by the cold source's teardown |

## Verification obligations

- A synchronous notifier `next` completes with zero source subscriptions.
- Notifier completion without a value leaves source delivery intact.
- Source and notifier errors both reach downstream while active.
- Downstream closure releases both connections without manufacturing completion.

## Common confusion

“Until the notifier finishes” is incorrect: it stops on **notifier next**. For a live stop Subject, calling only `complete()` will not terminate the source through `takeUntil`. Likewise, a stop value produced before subscribing may already be gone unless the notifier explicitly replays it.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/takeUntil)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeUntil.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
