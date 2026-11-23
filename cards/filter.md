# filter

**Family:** Value selection · **Reference:** RxJS 7.8.2 · **Scope:** Boolean predicate overload without deprecated `thisArg`

> Keep an arriving value when the supplied predicate accepts it, so downstream consumers receive only values relevant to their task.

## Type shape

```text
filter :: (T! → Bool!) → T* → T*
```

The predicate decides admission. This function-first description omits RxJS's zero-based source index and excludes type-guard and `Boolean` overloads.

## Intuition

Measurements arrive continuously, but a display should show only odd readings. The predicate expresses that rule. `filter` forwards an accepted reading unchanged and silently drops a rejected one, while continuing to observe later readings.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | `T! → T?`: forward the same value once if accepted, otherwise emit no value. |
| Whole connection | `T* → T*`: output is an ordered subsequence of successfully examined source values. |

Local `T?` means an upper bound of one `next`; it does not describe a separate Observable or promise completion. Across a connection, any number of values may pass, including none.

## Remembers

Each connection's independent index starts at zero and counts accepted and rejected values. No payload is retained. Captured state belongs to the predicate's closure.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize the index and connect to the source. |
| Source next | Evaluate the predicate with the value and index, advance the index, then forward the original value only if accepted. |
| Predicate throws | Send `error` and close this connection and its upstream subscription. |
| Source complete | Forward `complete`, even if no values passed; release upstream. |
| Source error | Forward `error`; release upstream. |
| Downstream close | Close upstream without sending `complete` or `error`. |

No timer, notifier, or inner subscription is involved.

## Time, cancellation, and sharing

- **Time:** Accepted values are forwarded while handling their source notifications. `filter` supplies no clock, scheduler, or deliberate delay.
- **Cancellation:** Closure releases this source subscription. Actual producer shutdown depends on its teardown and sharing ownership; prior external effects cannot be undone.
- **Sharing:** `filter` does not introduce sharing. Its index is independent per connection, even when the source is already shared.

## Worked trace

Illustrative cold source; times are milliseconds after connection opens, with setup before the first notification at zero. Use `isOdd(n)`, true exactly for odd integers.

| Time | Source event | Predicate index | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | 0 | `next(1)` |
| 10 | `next(2)` | 1 | None |
| 20 | `next(3)` | 2 | `next(3)` |
| 30 | `complete` | — | `complete` |

## Verification obligations

- Check identity and ordering of accepted payloads, and omission of rejected values.
- Check that rejected values still advance the predicate index.
- Check all-rejected completion, predicate failure, and upstream teardown on closure.

These are review/test obligations, not a claim that automated tests already exist.

## Common confusion

Rejection does not end the connection: later values can pass. Accepted source payloads pass unchanged; the predicate's boolean result is never the output payload.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/filter)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 error handling](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/OperatorSubscriber.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
