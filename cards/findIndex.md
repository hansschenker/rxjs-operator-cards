# findIndex

```text
Operator:       findIndex
Stream shape:   T* → (Nat | -1)?
State:          Nat — predicate index
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — first match emits its index and completes
Ownership:      One state per operator subscription
Lifecycle:      No match on completion emits -1 then completes; errors forward; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** findIndex(predicate), without deprecated thisArg; non-reentrant delivery for the cardinality model · **Format:** Detailed

> Publish the index of the first matching source value, or -1 after a completed search with no match.

## Type shape

```text
findIndex :: ((T × Nat × T*) → Bool) → T* → (Nat | -1)?
```

The predicate receives a value, its zero-based source index, and the source Observable. `Nat | -1` distinguishes a matching index from sentinel `-1`. This signature describes a Boolean predicate; the result remains an Observable.

The at-most-one model assumes non-reentrant delivery. RxJS 7.8.2 forwards a match before completing, so a result handler that synchronously pushes another matching source value can produce another result before closure.

## Intuition

A sequence of incoming records is being checked for the first invalid entry. The predicate expresses what invalid means. `findIndex` tells you where the first invalid record appeared, then stops listening. If the sequence finishes with every record passing validation, a predicate that searched for invalid records yields `-1`.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Test the value at its current index; reject silently or emit that index and complete. |
| Whole connection | At most one numeric payload: a matching index, or `-1` after a completed search with no match. |

Exactly one result requires reaching a match or normal source completion while connected, without a predicate failure. Early error or closure can produce no result. `-1` is an ordinary emitted value, not an error notification and not absence of an emission.

## Remembers

`Nat` is the next predicate index, initialized to zero per downstream connection. The implementation captures that index and increments it before invoking the predicate. All processed values count, including rejected ones; the first matching value does not receive index zero merely because it is the first result.

The `Action[]` summary selects no action for a rejection, or delivery of the captured index followed by completion for a match. RxJS 7.8.2 shares its search machinery with `find`, choosing index delivery instead of value delivery.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize index zero and connect to the source. |
| Source next, predicate false | Increment the counter and keep searching. |
| Source next, predicate true | Emit that value's captured index, then complete and close upstream. |
| Source complete without a match | Emit `-1`, then complete. |
| Source error | Forward the error without emitting `-1`. |
| Predicate throws | Error downstream and close upstream without emitting `-1`. |
| Downstream close | Close upstream silently; do not manufacture a search result. |

## Time, cancellation, and sharing

- **Time:** The result arrives when a match arrives, or when the source completes without one. No timer or scheduler is added; an unmatched, nonterminating source may never answer.
- **Cancellation:** Matching ends the owned source connection after delivering the index. Downstream closure also tears down that connection, without sending completion or undoing completed external work.
- **Sharing:** No sharing is introduced. Each connection counts the values it observes; a late connection to a live source starts its own count at zero.

## Worked trace

Illustrative times are virtual milliseconds from connection. A cold source schedules `4@10`, `7@20`, `9@30`, `12@40`, and completion at 50. Search with `value >= 8`. Source teardown cancels its remaining scheduled events.

| Time | Event | Output and state |
| --- | --- | --- |
| 0 | Connect | Next index is 0. |
| 10 | Reading 4, index 0 | Reject; next index is 1. |
| 20 | Reading 7, index 1 | Reject; next index is 2. |
| 30 | Reading 9, index 2 | Emit 2, complete, and close upstream. |
| 40–50 | Former scheduled events | No delivery through the closed connection. |

If the same finite source is searched with `value > 20`, it emits `-1` at 50 immediately before completing. An empty source likewise emits `-1` on completion.

## Verification obligations

- Check the trace's result 2, predicate indices, source argument, and teardown at 30.
- Check that empty and nonmatching finite sources each emit `-1` once.
- Check source/predicate errors and early closure without a sentinel.
- Check that a match at the first source value returns 0, not 1.

## Common confusion

This is an index among values observed by this operator, not an array position carried inside each payload or a count of matches. A preceding filter changes which values are counted. Unlike `find`, a successfully matched `undefined` payload remains distinguishable from no match: it has a nonnegative index, whereas no match is `-1`.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/findIndex)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/findIndex.ts)
- [RxJS 7.8.2 find implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/find.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
