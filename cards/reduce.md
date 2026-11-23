# reduce

**Family:** Accumulation · **Reference:** RxJS 7.8.2 · **Scope:** Explicit seed with the same seed and result type

> Accumulate arriving values privately and emit the final state when the source completes, providing one summary of a successfully finished input stream.

## Type shape

```text
reduce :: ((R! × T!) → R!) → R! → T* → R?
```

Supply an accumulator and seed. This explanatory signature omits RxJS's zero-based source index and excludes the unseeded overload.

## Intuition

Amounts arrive during a reporting period; their total is needed when it ends. Intermediate totals stay internal. The accumulator supplies the calculation; source completion triggers publication.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source next | `(R! × T!) → R!` updates remembered state but produces no output `next`. |
| Source complete | Emit the remembered state once, then complete, while the downstream connection remains open. |
| Whole connection | `T* → R?`: at most one result. Refine to `R!` under normal source completion, successful accumulation, and an open downstream connection. |

`R?` bounds output without promising progress. An endless source yields no final result. Empty normal completion emits the seed exactly once.

## Remembers

Each connection owns a seed-initialized state slot and zero-based index. Object seeds are not cloned; immutable replacement avoids cross-connection mutation.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize state and index, then connect upstream; emit nothing. |
| Source next | Accumulate state, value, and index; advance the index and retain the result without emitting. |
| Accumulator throws | Send `error`, close upstream, and emit no summary. |
| Source complete | Emit the state, including the seed for empty input; then complete and release upstream. |
| Source error | Forward `error` without flushing state; release upstream. |
| Downstream close | Close upstream without flushing state or sending a terminal notification. |

No timer, notifier, or inner subscription determines the result.

## Time, cancellation, and sharing

- **Time:** Source notifications drive accumulation; source completion triggers publication. `reduce` introduces no scheduler or clock.
- **Cancellation:** Closure disposes this upstream subscription. Producer shutdown depends on teardown and ownership. Closure cannot undo effects performed by the accumulator.
- **Sharing:** `reduce` introduces no sharing or replay. State slots are independent; mutable seed references and captured external state can remain shared.

## Worked trace

Illustrative cold source; milliseconds after connection, with setup preceding time-zero delivery. Accumulate by addition from seed `0`.

| Time | Source event | Remembered state | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | 1 | None |
| 10 | `next(2)` | 3 | None |
| 20 | `next(3)` | 6 | None |
| 30 | `complete` | 6 | `next(6)`, then `complete` |

## Verification obligations

- Check that source values update state without publishing intermediate results.
- Check seeded empty completion emits the seed; error and closure emit no summary.
- Check an uncompleted source produces no result and each connection starts independently.

These are review/test obligations, not claims of existing automated tests.

## Common confusion

Empty completion differs from endless silence. Seeded `scan` followed by `last` is not equivalent on empty input: `reduce` emits its seed, while `scan` supplies no state for `last` to select.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/reduce)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/reduce.ts)
- [RxJS 7.8.2 accumulation behavior](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/scanInternals.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
