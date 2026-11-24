# map

```text
Operator:       map
Stream shape:   T* → R*
State:          Nat — source index
Initial state:  0
Value step:     (Nat × T) → (Nat × R)
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; projection errors terminate; cancellation closes upstream silently
```

**Family:** Value transformation · **Reference:** RxJS 7.8.2 · **Scope:** Projection overload without deprecated `thisArg`

> Transform each arriving value with a supplied function, preserving its place in the stream so downstream consumers receive the representation they need.

## Type shape

```text
map :: (T! → R!) → T* → R*
```

Supply a projection between payload types. This explanatory signature omits RxJS's zero-based source index; it is not a literal TypeScript declaration.

## Intuition

A sensor sends numbers over time; a display needs each doubled. The projection supplies the calculation. `map` applies it without knowing the payloads' business meaning.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | `T! → R!`: a projection that returns normally produces one output value while the connection remains open. |
| Whole connection | `T* → R*`: preserve the order and count of successfully processed source values. |

These counts concern `next` values. An empty source remains empty. Errors or downstream closure can end processing; the signature promises neither future input nor eventual completion.

## Remembers

Each connection owns a source index, initially zero. There is no value buffer or accumulated result. The supplied function may capture external state, which `map` neither isolates nor clones.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize this connection's index and connect to the source. |
| Source next | Call the projection with the value and current index, advance the index, and forward its result. |
| Projection throws | Send `error` and close this connection, including its upstream subscription. |
| Source complete | Forward `complete`; release the upstream subscription. |
| Source error | Forward `error`; release the upstream subscription. |
| Downstream close | Close the upstream subscription without sending a terminal notification. |

No timer, notifier, or inner subscription participates.

## Time, cancellation, and sharing

- **Time:** Values follow source notification timing. The projection runs during each source notification; `map` introduces no scheduler or deliberate delay.
- **Cancellation:** Teardown releases this upstream connection. Whether an external producer stops depends on its ownership and teardown. Closure does not undo effects already performed by the projection.
- **Sharing:** `map` introduces no sharing. Connections have independent indexes, even when they observe an existing shared source.

## Worked trace

Illustrative cold source; times are milliseconds after its connection opens. Connection setup precedes the first value at time zero. Use the pure projection `double(n) = 2 × n`.

| Time | Source event | Projection index | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | 0 | `next(2)` |
| 10 | `next(2)` | 1 | `next(4)` |
| 20 | `next(3)` | 2 | `next(6)` |
| 30 | `complete` | — | `complete` |

## Verification obligations

- Check one output per successful projection, in source order.
- Check that a throwing projection terminates processing and tears down upstream.
- Check index reset on a second connection and no delivery after closure.

These are review/test obligations, not claims of an existing automated suite.

## Common confusion

Returning an Observable produces an Observable payload: `(R*)*`. `map` does not connect to it or choose a flattening policy. Returning an array similarly produces one array payload.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/map)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/map.ts)
- [RxJS 7.8.2 error handling](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/OperatorSubscriber.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
