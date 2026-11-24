# skipLast

```text
Operator:       skipLast
Stream shape:   T* → T*
State:          T[] — bounded queue of the newest n values
Initial state:  []
Value step:     (T[] × T) → (T[] × Option<T>) — release oldest once n newer positions are available
Ownership:      One state per operator subscription
Lifecycle:      Completion discards the tail then completes; error forwards; cancellation discards and closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skipLast(n), with a nonnegative integer n · **Format:** Detailed

> Hold back n values so that the final n never reach downstream.

## Type shape

```text
skipLast :: Nat → T* → T*
```

The fixed nonnegative integer `n` counts how many newest values remain withheld. The output contains individual source payloads, not arrays. This explanatory function-first signature does not specify elapsed-time delay; fractional, negative, and non-finite arguments are outside the declared scope.

## Intuition

A recording contains a closing tail you do not want to publish. Keep the newest two arrivals pending. When a third value arrives, the oldest can safely be released because two newer positions now follow it. At completion, discard the two still pending.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source next, `n > 0` | Retain the value; once `n` newer positions exist for the oldest retained value, emit that oldest value. |
| Whole connection | Preserve source order while withholding the newest `n`. For an uninterrupted connection, a source that normally completes after `k` values yields `max(k − n, 0)` outputs. |

`Option<T>` represents the local possibility of one release; `None` produces no notification. A source can keep producing and releasing values forever without completing. A short source can complete with no output. Neither a first release nor completion is guaranteed by the type.

## Remembers

The header's `T[]` is a behavioral queue model of the pinned implementation's ring buffer. For `n > 0`, remember at most `n` payloads; the implementation also tracks a position counter. Once full, replace the oldest slot before forwarding its previous value.

The queue holds references, not copies of payload objects. Each connection owns this memory independently. For `n = 0`, the implementation mirrors the source directly and requires no holding buffer. No source-value history beyond the withheld suffix is needed.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Connect to the source; establish the holding buffer for a positive count. |
| Source next while buffer is filling | Retain the value and emit nothing. |
| Source next after buffer is full | Store the new value, then forward the displaced oldest value. |
| Source next, `n = 0` | Forward immediately without holding the value. |
| Source complete | Discard the held tail and complete; release upstream and memory. |
| Source error | Forward the error without flushing the held tail; release resources. |
| Downstream close | Close upstream and discard pending values without completion or error. |

Already delivered values remain delivered after an error or closure.

## Time, cancellation, and sharing

- **Time:** Subsequent arrivals trigger release. The wait is measured in values, not milliseconds; a quiet source can leave a value pending indefinitely. No scheduler or timer is introduced.
- **Cancellation:** Closure stops delivery and runs owned upstream teardown. It does not release the withheld tail, undo prior effects, or necessarily stop a producer owned elsewhere.
- **Sharing:** No sharing is added. Separate connections can hold different tails of the same live source because they may begin observing at different times.

## Worked trace

Illustrative cold source; times are milliseconds from connection, with setup before its first value at zero. Apply `skipLast(2)`. Queue contents below use the behavioral order from oldest to newest.

| Time | Source event | Retained queue after event | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | `[1]` | None |
| 10 | `next(2)` | `[1, 2]` | None |
| 20 | `next(3)` | `[2, 3]` | `next(1)` |
| 30 | `next(4)` | `[3, 4]` | `next(2)` |
| 40 | `next(5)` | `[4, 5]` | `next(3)` |
| 50 | `complete` | Discarded | `complete` |

## Verification obligations

- Check the first release occurs on arrival `n + 1`, before completion.
- Check `skipLast(0)` mirrors and still subscribes to the source.
- Check empty/short completion and errors do not flush the tail.
- Check irregular timing follows arrivals rather than a fixed delay.
- Check closure and independent buffers for separate connections.

These are behavioral obligations, not an assertion of exhaustive test coverage.

## Common confusion

`skipLast` can emit before it knows when the source will end. It only needs enough newer values to prove an older one is outside the pending tail. It differs from `takeLast`, which emits the retained suffix only at normal completion, and from `skip`, which discards an initial prefix.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/skipLast)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skipLast.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
