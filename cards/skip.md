# skip

```text
Operator:       skip
Stream shape:   T* → T*
State:          Nat — zero-based source index
Initial state:  0
Value step:     (Nat × T) → (Nat × Option<T>) — admit when index ≥ n
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; cancellation closes upstream silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skip(n), with a nonnegative integer n · **Format:** Detailed

> Discard the first n source values, then forward every later arrival.

## Type shape

```text
skip :: Nat → T* → T*
```

The fixed argument `n` counts source `next` values, including repeated equal payloads. It does not count elapsed time or terminal notifications. This function-first description is explanatory notation, not a literal TypeScript declaration; fractional, negative, and non-finite arguments are outside the scope.

## Intuition

A device sends two startup readings before its ordinary measurements. After connecting, discard those first two arrivals and display everything that follows. `skip` counts arrivals; it does not inspect whether a reading is accurate or meaningful. A later connection starts counting from its own first observed value.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Advance the index and return `None` before position `n`, otherwise `Some(value)` for unchanged forwarding. |
| Whole connection | Remove the first `n` observed values. For `k` source values followed by normal completion, an uninterrupted connection delivers `max(k − n, 0)` values. |

`Option<T>` describes a local admission decision; `None` does not emit an `undefined` payload. There is no nonempty-output guarantee: the source can complete early, fail, or remain silent. Once the threshold is reached, later values continue passing until termination or closure.

## Remembers

`Nat` is the zero-based index of the next source value. It starts at zero and advances on every examined value, including discarded values. The comparison uses the current index, so `skip(2)` first admits index `2`, the third arrival.

The count is a fixed operator argument. Each connection owns its index; no payload history or user projection is retained. Observing a shared producer does not merge these counters. The pinned implementation expresses the rule through `filter` with its source index.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize index zero and subscribe to the source, including when `n = 0`. |
| Source next before threshold | Advance the index and discard the value. |
| Source next at or after threshold | Advance the index and forward the same payload. |
| Source complete | Complete downstream, even if the threshold was never reached; release upstream. |
| Source error | Forward the error and release upstream; skipping values does not suppress errors. |
| Downstream close | Release upstream without emitting a value or terminal notification. |

There is no timer, notifier, inner subscription, or user-supplied predicate.

## Time, cancellation, and sharing

- **Time:** Admitted values retain source notification timing. No scheduler or deliberate delay is introduced; discarding two arrivals does not mean waiting two milliseconds.
- **Cancellation:** Closing stops delivery through this connection and runs its upstream teardown. It does not undo earlier effects or necessarily stop an independently owned producer.
- **Sharing:** `skip` creates no sharing boundary. Each connection counts only values it actually observes; a live source may have produced earlier values before that connection existed.

## Worked trace

Illustrative cold source; times are milliseconds after connection, with setup before the first value at zero. Apply `skip(2)` to five arrivals and normal completion.

| Time | Source event | Index after event | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | 1 | None |
| 10 | `next(2)` | 2 | None |
| 20 | `next(3)` | 3 | `next(3)` |
| 30 | `next(4)` | 4 | `next(4)` |
| 40 | `next(5)` | 5 | `next(5)` |
| 50 | `complete` | Connection ended | `complete` |

## Verification obligations

- Check the boundary index and unchanged order of admitted values.
- Check `skip(0)` still subscribes and forwards everything.
- Check empty/short completion and an error before the threshold.
- Check cancellation and independent counters on a shared source.
- Check repeated equal values count as separate arrivals.

These are obligations, not a claim that every scenario has been tested.

## Common confusion

`skip(0)` is not an empty result: it observes the source normally. `skip` removes a prefix, whereas `skipLast` holds back a suffix and shifts delivery until later values arrive. Neither operation decides admission from the business meaning of a payload.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/skip)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skip.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
