# shareReplay

**Family:** Sharing · **Reference:** RxJS 7.8.2 · **Scope:** `shareReplay({ bufferSize: 1, refCount: true })`, with an unbounded replay time window.

> Share one active upstream execution among connected observers and give each new observer the latest buffered value.

## Type shape

```text
shareReplay(latest = 1, refCount = true) :: T* → T*
```

The named settings above are explanatory notation; the scope line gives the actual API call. Payload type and broad cardinality remain unchanged, while connection ownership changes.

## Intuition

Several views can read the same computed state. They should share its upstream work, and a newly connected view should receive the latest available state immediately.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source value | Cache it and deliver it to each currently connected observer |
| One new observer | Replay the latest buffered value, if one exists |
| Whole connection | `T*`; a subscriber may receive replay plus later source values |

The operator does not create an initial value when the source has emitted none.

## Remembers

The resulting shared Observable owns a replay subject, its one-value buffer, current upstream connection, observer reference count, and terminal status. This state is shared at this boundary rather than duplicated for every observer.

## Event → action

| Event | State change and action |
| --- | --- |
| First observer connects to a fresh active instance | Establish the upstream connection |
| Additional observer connects | Replay the buffered value, then observe future values |
| Source next | Replace the cache and multicast the value |
| Source complete | Complete current observers; retain the value and completion for later observers |
| Source error | Reset shared state, then error current observers through the old subject; a new observer can establish a fresh source connection |
| One observer closes | Remove that observer and reduce the reference count |
| Last observer closes while upstream is unfinished | Close upstream and reset the cache/state |
| Observer connects after normal completion | Replay the completed cache and complete; do not restart upstream |

## Time, cancellation, and sharing

- **Time:** Source delivery supplies new-value timing. Replay in this scope occurs synchronously on connection. This card specifies no time-based expiry.
- **Cancellation:** One observer leaving does not cancel work still owned by remaining observers. The last observer closes an unfinished upstream connection because `refCount` is true. External work stops only if its teardown supports that.
- **Sharing:** Sharing is the purpose of this operator. Put the boundary after the state/transformation work that should be shared.

## Worked trace

A cold source is opened at time 0. Times are relative to that shared connection.

| Time | Event | Result |
| --- | --- | --- |
| 0 | Observer A connects | One upstream execution starts |
| 10 | Source emits x | A receives x |
| 15 | Observer B connects | B immediately receives cached x |
| 20 | Source emits y | A and B receive y |
| 25 | A closes | B keeps upstream connected |
| 30 | Source completes | B receives complete; y stays cached |
| 40 | Observer C connects | C receives y then complete; no new upstream execution |

In a different run, if every observer leaves before time 30, the unfinished connection and cache reset. A later observer starts a new source execution.

## Verification obligations

- Concurrent observers cause only one upstream subscription.
- Late observers receive the latest buffered value.
- Last-observer closure resets an unfinished execution.
- Normal completion retains replay; error permits a fresh execution later.

## Common confusion

`shareReplay(1)` uses `refCount: false`; it does not have this card's last-observer cancellation rule. Conversely, `refCount: true` does not reset a normally completed cache. `scan` accumulates state; `shareReplay` shares/replays the result.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/shareReplay)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/shareReplay.ts)
- [Underlying share implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/share.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
