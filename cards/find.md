# find

```text
Operator:       find
Stream shape:   T* → (T | Undefined)?
State:          Nat — predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — first match emits and completes
Ownership:      One state per operator subscription
Lifecycle:      No match on completion emits undefined then completes; errors forward; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** find(predicate), without deprecated thisArg; non-reentrant delivery for the cardinality model · **Format:** Detailed

> Emit the first matching value, or an actual undefined value if the source completes without one.

## Type shape

```text
find :: ((T × Nat × T*) → Bool) → T* → (T | Undefined)?
```

The predicate receives the current value, its zero-based index, and the source Observable supplied to this operator. This function-first signature describes the ordinary Boolean-predicate overload. `Undefined` names the actual JavaScript payload `undefined`; `?` limits the entire output to at most one `next`.

The at-most-one model assumes non-reentrant delivery. RxJS 7.8.2 forwards a match before completing, so a result handler that synchronously pushes another matching source value can produce another result before closure.

## Intuition

A sensor produces readings over time. You want the first reading at or above a threshold, then the search should stop. The predicate decides which reading qualifies. `find` handles searching, publishing the first match, and closing the source connection. If the sensor finishes without a match, the search publishes `undefined`.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Test the value; a rejection emits nothing, while a match emits that value and completes. |
| Whole connection | At most one payload: either the first match or `undefined` on normal source completion without a match. |

If a match or normal source completion is reached while connected, with no predicate failure, exactly one payload is delivered. A source error, predicate error, or earlier downstream closure can prevent that result. A never-completing source with no match can stay silent forever.

## Remembers

`Nat` is the next predicate index, initially zero. For each source value, the implementation saves the current index, increments the counter, and passes the saved index to the predicate. Rejected values therefore count too. Each downstream connection has its own counter.

`Action[]` is an ordered list: rejection selects no output action; acceptance selects delivery of the matching value followed by completion. No matching value needs to remain buffered: the search terminates as soon as it finds one.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize index zero and connect to the source. |
| Source next, predicate false | Advance the index and continue searching. |
| Source next, predicate true | Advance the index, emit the value, and complete; closing the result tears down upstream. |
| Source complete without a match | Emit `undefined`, then complete. |
| Source error | Forward the error without emitting a fallback. |
| Predicate throws | Error downstream and close upstream; do not emit a fallback. |
| Downstream close | Close upstream silently; no fallback or completion is sent. |

## Time, cancellation, and sharing

- **Time:** Testing and delivery follow source notifications. No timer or scheduler is introduced; a synchronous source can finish the search synchronously.
- **Cancellation:** A match ends this source subscription immediately after delivering its result. Teardown can stop owned producer work, but does not undo effects or necessarily stop a shared producer.
- **Sharing:** No sharing is introduced. Observers connected separately run independent searches and maintain independent indices, even if their source is shared.

## Worked trace

Illustrative times are virtual milliseconds from connection. A cold source schedules readings `4@10`, `7@20`, `9@30`, `12@40`, and completion at 50. The predicate is `value >= 8`; source teardown cancels undelivered scheduled events.

| Time | Event | Output and state |
| --- | --- | --- |
| 0 | Connect | Next index is 0. |
| 10 | Reading 4, index 0 | Reject; next index is 1. |
| 20 | Reading 7, index 1 | Reject; next index is 2. |
| 30 | Reading 9, index 2 | Emit 9, complete, and close upstream. |
| 40–50 | Former scheduled events | No delivery: the source connection has closed. |

## Verification obligations

- Check the trace, predicate indices, source argument, and teardown time.
- Check empty and nonempty completed searches with no match: each emits one `undefined`.
- Check source/predicate failures and early closure: no no-match fallback.
- Check that a matching `undefined` value is still one delivered payload.

## Common confusion

`find` does not behave like `filter(predicate).pipe(take(1))` when the source completes without a match: that pipeline completes empty. It also differs from `first(predicate)`, which errors without a match unless a default is provided. If `T` itself contains `undefined`, the output payload alone cannot distinguish a matching `undefined` from a completed search with no match; an index or explicitly tagged result preserves that distinction.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/find)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/find.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
