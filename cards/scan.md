# scan

**Family:** Accumulation · **Reference:** RxJS 7.8.2 · **Scope:** Explicit seed with the same seed and result type

> Remember an accumulated state and emit its next version whenever a source value updates it, making changing state available throughout the connection.

## Type shape

```text
scan :: ((R! × T!) → R!) → R! → T* → R*
```

Supply an accumulator, then an initial state. This explanatory signature omits RxJS's zero-based source index and excludes the unseeded overload.

## Intuition

Amounts arrive over time. A running total updates after each amount, making progress visible. The accumulator supplies the domain rule; `scan` remembers and forwards successive states.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | `(R! × T!) → R!`: compute and emit one new state if accumulation returns normally and the connection stays open. |
| Whole connection | `T* → R*`: one state output per successfully processed source value; the seed alone produces no output. |

Cardinality counts `next` values. Empty completion emits no state; the type promises neither nonempty output nor eventual completion.

## Remembers

Each connection owns a seed-initialized state slot and zero-based index. Seed objects are not cloned: slots can reference the same object. Pure accumulation with immutable replacement avoids cross-connection mutation.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Set state to the seed, initialize the index, and connect upstream; emit nothing yet. |
| Source next | Pass state, value, and index to the accumulator; advance the index, retain the returned state, and emit it. |
| Accumulator throws | Send `error` and close this connection and upstream; emit no replacement state for that event. |
| Source complete | Forward `complete` without repeating the last state; release upstream. |
| Source error | Forward `error`; release upstream. Previously delivered states remain delivered. |
| Downstream close | Close upstream without emitting a final state or terminal notification. |

There are no timers, notifiers, or inner connections.

## Time, cancellation, and sharing

- **Time:** State updates follow source notification timing. `scan` introduces no scheduler or deliberate delay.
- **Cancellation:** Closure disposes this source subscription. It does not reverse external mutations or effects; producer shutdown depends on the producer's teardown.
- **Sharing:** State is normally per connection. `scan` neither shares execution nor replays a previous state to new connections; an explicit sharing boundary changes ownership.

## Worked trace

Illustrative cold source; milliseconds after connection, with setup preceding time-zero delivery. Accumulate by addition from seed `0`.

| Time | Source event | Remembered state afterward | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | 1 | `next(1)` |
| 10 | `next(2)` | 3 | `next(3)` |
| 20 | `next(3)` | 6 | `next(6)` |
| 30 | `complete` | Connection ended | `complete` |

## Verification obligations

- Check running states and zero-based accumulator indexes.
- Check no seed emission on connection or empty completion.
- Check independent connection state, accumulator failure, and teardown after closure.

These are review/test obligations, not assertions of existing automated coverage.

## Common confusion

Retaining state internally does not make `scan` a replaying source. Completion also adds no final duplicate: the last accumulated state was already emitted when its source value arrived.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/scan)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/scan.ts)
- [RxJS 7.8.2 accumulation behavior](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/scanInternals.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
