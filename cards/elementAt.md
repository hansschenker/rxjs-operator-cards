# elementAt

```text
Operator:       elementAt
Stream shape:   T* → T?
State:          Nat — zero-based source index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — emit at requested index, then complete
Ownership:      One state per operator subscription
Lifecycle:      Match completes early; short source errors on completion; source error forwards; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** elementAt(index), with a nonnegative integer index and no default · **Format:** Detailed

> Select the source value at one zero-based position and finish immediately.

## Type shape

```text
elementAt :: Nat → T* → T?
```

The argument is a fixed zero-based source position. Index `0` requests the first arrival; index `2` requests the third. This card covers nonnegative integer indexes without a default. Output has at most one `next`; success depends on that position actually arriving before error or cancellation.

## Intuition

You need the second reading after connecting. Ignore the first arrival, emit the second immediately, and finish. The operator counts arrivals without inspecting their meaning. It need not collect the sequence, wait for its end, or receive later readings.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Compare its zero-based index with the requested position; ignore earlier values, then emit the selected value and complete. |
| Whole connection | Exactly one result if the requested position arrives before error or downstream closure. A normally completing source that is too short produces `ArgumentOutOfRangeError`. |

An empty source has no value even at index `0`. A source that pauses forever before the target neither produces a result nor proves the target absent. The operator requires normal completion to determine that a nonnegative requested index is beyond the available sequence.

## Remembers

`Nat` is the next source index, initially zero. It advances for each incoming value; the requested index remains a fixed operator argument. No earlier payloads are retained. `Action[]` records the ordered selection actions: emit the matching payload, then complete. Each operator subscription owns its own counter and upstream connection, including when several connections observe a shared source.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Reset the source index to zero and subscribe upstream. |
| Source next before target | Advance the index; emit nothing. |
| Source next at target | Emit that payload, complete downstream, and close upstream. |
| Source complete before target | Send an `ArgumentOutOfRangeError` notification. |
| Source error before target | Forward the original error, not an out-of-range replacement. |
| Downstream close | Close upstream without a value, error, or completion notification. |

## Time, cancellation, and sharing

- **Time:** Source arrivals supply timing. No scheduler, timer, or delay is added. A synchronous source can reach the requested index during the subscription call.
- **Cancellation:** Selection closes this upstream subscription early. A cooperative synchronous producer can stop at once. Closing a connection does not undo earlier work or necessarily stop an independently shared producer.
- **Sharing:** No sharing is introduced. Indexes are relative to each subscription's observed values, not a global position in a shared producer's history. No replay of missed values is added.

## Worked trace

Illustrative cold source connected at `t = 0`; apply `elementAt(1)`. Times are virtual milliseconds from connection. Source plan: `a@10`, `b@20`, `c@40`, complete at `50`.

| Time | Event or source plan | Source index | Output |
| --- | --- | --- | --- |
| 10 | Source `a` | `0` | None |
| 20 | Source `b` | `1` | `b`, then complete |
| 20 | Upstream teardown | — | Remaining source work cancelled |
| 40, 50 | Original source plan | — | No further delivery |

The accepted value precedes completion and teardown in the same virtual frame. Selecting index `0` would instead finish on the first arrival.

## Verification obligations

- Reproduce the trace and source teardown at the selected position.
- Verify zero-based indexing, including early closure of a synchronous source at index `0`.
- Check empty and short sources for `ArgumentOutOfRangeError` on completion.
- Preserve source errors and actual `undefined` payloads.
- Confirm cancellation before the target is silent.

## Common confusion

The index counts values, not milliseconds. Reaching the target succeeds immediately; source completion is needed only to establish that a validly requested position was never reached.

Outside the declared scope, a negative index throws `ArgumentOutOfRangeError` **when constructing the operator**, before any subscription. This differs from the error notification produced by a short source. A default does not repair a negative index.

Supplying a second default argument changes only normal short-source completion into one default emission followed by completion. An explicit `undefined` counts as supplied. Defaults do not recover source errors or resolve a source that never finishes.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/elementAt)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/elementAt.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 take implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/take.ts)
- [RxJS 7.8.2 throwIfEmpty implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
