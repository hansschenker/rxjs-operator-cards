# takeWhile

```text
Operator:       takeWhile
Stream shape:   T* → T*
State:          Nat — predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — emit if predicate succeeds; otherwise complete
Ownership:      One state per operator subscription
Lifecycle:      First rejection completes and closes upstream; errors forward; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** takeWhile(predicate), inclusive = false · **Format:** Detailed

> Forward the initial run of values accepted by the predicate, then end at the first rejection.

## Type shape

```text
takeWhile :: (T! → Bool!) → T* → T*
```

The predicate decides whether the initial accepted run continues. This explanatory function-first signature omits its zero-based index parameter. The scoped behavior excludes the first rejected value; the separately configured inclusive variant is discussed only as a comparison.

## Intuition

Readings arrive during a run. Keep its initial stretch below a threshold, and finish the result as soon as a reading reaches that threshold. Even if a later reading would qualify again, this connection has already ended. The predicate supplies the threshold rule; the operator applies its stopping policy.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| Predicate returns true | Emit the original source value and keep observing. |
| Predicate first returns false | Omit that value, complete downstream, and close upstream. |
| Whole connection | Deliver the initial accepted prefix, in source order, until rejection or another termination event. |

A first-value rejection produces zero values followed by completion. If every value qualifies, normal source completion ends the result. A silent or indefinitely qualifying source need not complete. The type shape promises no particular output count or progress.

## Remembers

`Nat` is the next predicate index, initially zero. It advances for each tested source value, including the first rejection. There is no stored payload or reopening gate: closure after rejection belongs to the lifecycle.

`Action[]` describes ordered decisions such as emitting a value, or completing and releasing upstream. A thrown predicate exception instead selects the error path. Each connection has its own index; external mutable state captured by the supplied predicate is not automatically isolated.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize index zero and connect to the source. |
| Source next accepted | Call the predicate, advance the index, and forward the same value. |
| Source next rejected | Advance the index, omit the value, complete downstream, and close upstream. |
| Predicate throws | Error downstream and close upstream; do not turn the exception into ordinary rejection. |
| Source complete | Forward completion if still open; release upstream. |
| Source error | Forward the error if still open; release upstream. |
| Downstream close | Close upstream without a terminal notification or further predicate calls. |

There is no timer, notifier, or inner connection.

## Time, cancellation, and sharing

- **Time:** The predicate is evaluated on source arrivals. Accepted values and rejection-triggered completion occur while handling those arrivals; no scheduler or delay is introduced. Changing external predicate state during source silence does not itself trigger evaluation.
- **Cancellation:** Rejection closes this upstream subscription. External producer shutdown depends on teardown and ownership; delivered values and completed effects are not reversed.
- **Sharing:** No sharing is added. Each downstream connection independently decides its stopping point, even when observing an existing shared source.

## Worked trace

Illustrative cold source, with times in milliseconds from connection. Setup precedes its first value at zero. Use the predicate `value < 3`. Source work scheduled after closure is canceled by this example producer's teardown.

| Time | Source event or plan | Predicate index/result | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | `0 / true` | `next(1)` |
| 10 | `next(2)` | `1 / true` | `next(2)` |
| 20 | `next(3)` | `2 / false` | `complete`; upstream closes |
| 30 | Planned `next(1)` | Not called | None |
| 40 | Planned `next(4)` | Not called | None |
| 50 | Planned source completion | Not observed | None |

## Verification obligations

- Check rejection is omitted and completion precedes the source's planned ending.
- Check first rejection, all-accepted completion, and empty input.
- Check zero-based indexes and no predicate calls after rejection or closure.
- Check source errors and thrown predicate errors retain their error outcome.
- Check separate connections can reach different boundaries on a live source.

These obligations are not a claim that every listed scenario has been tested.

## Common confusion

`filter` can reject a value and later accept another; `takeWhile` permanently ends at the first rejection. The explicit `inclusive = true` variant additionally emits that first rejected value before completing. It still ends there; inclusion does not allow later values through.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/takeWhile)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeWhile.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
