# last

```text
Operator:       last
Stream shape:   T* → T?
State:          Option<T> × Nat — latest match and predicate index
Initial state:  (None, 0)
Value step:     (S × T) → (S × Action[]) — replace candidate on a match; emit nothing yet
Ownership:      One state per operator subscription
Lifecycle:      Completion emits latest match or errors if absent; source errors forward; cancellation discards silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** last(predicate), without a default value · **Format:** Detailed

> Remember the latest matching value and publish it only when the source completes.

## Type shape

```text
last :: (T × Nat × T* → Bool) → T* → T?
```

The predicate receives a value, its zero-based index, and the source Observable. Output has at most one `next`, without guaranteeing arrival or completion. No default is supplied.

## Intuition

During a measurement session, keep the latest valid reading. A newer valid reading replaces the candidate; an invalid reading leaves it untouched. Only normal completion establishes which candidate was last. Publish that reading then; candidate changes produce no output.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Evaluate the predicate. Replace the candidate on acceptance; otherwise retain it. Emit nothing yet. |
| Whole connection | While downstream remains connected, normal completion emits the candidate once, or errors with `EmptyError` when no candidate exists. |

Even a known candidate cannot be published early: a later matching value may replace it. A source that keeps running without completing has no final result. Source error or downstream closure does not establish a successful ending and therefore does not flush the candidate.

## Remembers

`S = Option<T> × Nat` stores the latest matching payload and the next predicate index. Initially it is `(None, 0)`. `Some(undefined)` and `Some(null)` are genuine candidates, distinct from `None`. The index advances on each source value, including rejections. Memory belongs to each operator connection; it retains at most one matching payload rather than the whole source history.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Clear the candidate, reset the index, and subscribe upstream. |
| Source next, predicate true | Advance the index and replace the candidate; emit nothing. |
| Source next, predicate false | Advance the index; keep the current candidate. |
| Predicate throws | Error downstream and discard the candidate. |
| Source complete with candidate | Emit its payload, then complete downstream. |
| Source complete without candidate | Error with `EmptyError`, whether the source was empty or all values were rejected. |
| Source error | Forward the original error and discard any candidate. |
| Downstream close | Close upstream and discard the candidate silently. |

## Time, cancellation, and sharing

- **Time:** The source supplies timing; no scheduler or timer is introduced. The candidate's arrival time differs from its output time: delivery waits for completion.
- **Cancellation:** Finding a match does not cancel upstream, because the operator must continue checking for newer matches. Error or downstream closure ends the connection without a final value. Closing does not undo external work already performed.
- **Sharing:** No sharing boundary is introduced. Each connection owns its own candidate and index; a late observer of a shared source can consider only values it actually receives.

## Worked trace

Illustrative cold source, connected at `t = 0`, with predicate `value % 2 === 0`. Times are virtual milliseconds relative to connection.

| Time | Source event | Candidate afterward | Output |
| --- | --- | --- | --- |
| 10 | `1`, predicate index `0` | `None` | None |
| 20 | `4`, predicate index `1` | `Some(4)` | None |
| 40 | `6`, predicate index `2` | `Some(6)` | None |
| 50 | Complete | Released | `6`, then complete |

The source remains connected through `50`. Replacing completion with an error would produce only that error; cancellation before completion would produce no result or terminal notification.

## Verification obligations

- Reproduce the trace, indexes, source argument, and completion-time delivery.
- Check empty input and nonempty rejected input for `EmptyError`.
- Check source/predicate errors after a candidate exists; neither may flush it.
- Preserve an actual `undefined` candidate as a value.
- Confirm a nonterminating source stays silent and cancellation releases its candidate.

## Common confusion

`last` is not a running latest-value stream: remembering a new candidate emits nothing. Without a predicate, every source value replaces the candidate, but output still waits for completion.

Outside this card's declared scope, a supplied second default argument replaces the missing-match error with a default emission and completion. An explicit `undefined` default counts as supplied. Defaults do not recover source errors, suppress predicate exceptions, or produce a result from a source that never completes.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/last)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/last.ts)
- [RxJS 7.8.2 takeLast implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/takeLast.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 throwIfEmpty implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
