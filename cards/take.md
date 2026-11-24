# take

```text
Operator:       take
Stream shape:   T* → T* (at most n values)
State:          Nat — values seen
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — emit while admitted; complete at n
Ownership:      One counter and upstream subscription per operator subscription
Lifecycle:      n = 0 skips source; limit/source completion completes; errors forward; cancellation closes silently
```

**Family:** Count selection · **Reference:** RxJS 7.8.2 · **Scope:** `take(n)` for a nonnegative integer `n`

> Forward the first `n` arriving values unchanged, then complete and close this connection to the source.

## Type shape

```text
take :: NonnegativeInteger → T* → T*
take(1) :: T* → T?
```

The supplied number counts `next` values. It does not count completion, errors, or elapsed time. The output has at most `n` values; `T?` does not promise that one value will arrive. Fractional, negative, and non-finite arguments are outside this card's scope.

## Intuition

You want the first three readings after connecting to a sensor. Every reading passes through immediately until the third arrives. That third reading passes through too; the output then completes without waiting for the sensor itself to finish. The operator needs no knowledge of what a reading contains.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Count an arriving value; forward it while the limit permits. After forwarding the limiting value, complete. |
| Whole connection | At most `n` values, in source order. If a source normally produces `k` values and downstream remains connected, the output contains `min(n, k)` values. |

A positive limit does not guarantee progress. A source that neither produces enough values nor terminates can leave the output connected indefinitely.

## Remembers

Each connection remembers its own number of values seen and the supplied limit. There is no collection of past payloads. A second connection gets a new counter, even when both connections observe the same shared source.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | For `n = 0`, complete immediately without subscribing to the source. Otherwise initialize the counter and connect. |
| Source next | Increment the counter; forward an admitted value. On reaching the limit, complete downstream and release the upstream connection. |
| Source complete | Complete downstream if still connected, even when fewer than `n` values arrived. |
| Source error | Forward the error if still connected; no minimum output count is guaranteed. |
| Downstream close | Release the upstream connection. Send no completion notification. |

## Time, cancellation, and sharing

- **Time:** Delivery follows the source; no scheduler or clock is introduced. A synchronous source can reach the limit during the subscription call.
- **Cancellation:** Reaching the count closes this upstream subscription. It does not undo delivered values or completed external work. An independently running producer may continue.
- **Sharing:** `take` creates no sharing boundary. A shared source retains its existing connection and lifetime policy.

## Worked trace

Illustrative cold source, connected at `t = 0`; milliseconds are relative to that connection. Its planned sequence is `a@10`, `b@20`, `c@40`, complete at `50`. Apply `take(2)`.

| Time | Event or source plan | Output and state |
| --- | --- | --- |
| 0 | Connect | No value; seen `0` |
| 10 | `a` | `a`; seen `1` |
| 20 | `b` | `b`, then complete; upstream closes |
| 40, 50 | Remaining source work | Cancelled by this cold source's teardown; no output |

## Verification obligations

- `take(0)` completes while a source-subscription counter remains zero.
- An empty source completes normally; a short source forwards all its values.
- The limiting value precedes completion, and no later source value escapes.
- An error before the limit remains an error.

## Common confusion

`take(1)` means **at most one**, not **require one**. On an empty normally completing source, it completes silently. `first()` without a default instead reports an `EmptyError`. A count limit also says nothing about how long the first value takes to arrive.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/take)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/take.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
