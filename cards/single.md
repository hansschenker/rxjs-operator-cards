# single

```text
Operator:       single
Stream shape:   T* → T?
State:          Option<T> × Bool × Nat — candidate, any-source-value flag, predicate index
Initial state:  (None, false, 0)
Value step:     (S × T) → (S × Action[]) — retain first match; error on second match
Ownership:      One state per operator subscription
Lifecycle:      Completion emits sole match; missing/extra matches error; cancellation closes without a result
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** single(predicate), with optional predicate · **Format:** Detailed

> Require exactly one matching source value, and publish it after the source completes.

## Type shape

```text
single :: (T × Nat × T* → Bool) → T* → T?
single() :: T* → T?
```

With a predicate, count matching arrivals. Without one, every source value matches. The predicate receives value, zero-based source index, and source Observable. `T?` states the output bound; successful completion requires exactly one match, not merely at most one.

## Intuition

A search should identify one qualifying record. Store the first candidate, but keep listening: a second qualifying record disproves uniqueness. Publish only after completion confirms one match. This validates the whole sequence; immediately publishing the first match would bypass that validation.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Reject nonmatches; remember the first match; error immediately when a second match arrives. |
| Whole connection | Produce one value only when normal source completion confirms exactly one match. Empty, missing-match, and multiple-match cases have different errors. |

Repeated equal payloads still count as separate matches. Uniqueness concerns arriving values, not distinct payload equality. A candidate from a source that never completes remains unpublished; downstream cancellation cannot certify that it was the only match.

## Remembers

`S = Option<T> × Bool × Nat` holds the candidate, whether any source value has arrived, and the next predicate index. Initially it is `(None, false, 0)`. The Boolean becomes true even for rejected values; it distinguishes an empty source from a nonempty source with no match. `Some(undefined)` is present, unlike `None`. The index counts predicate calls when supplied. Every subscription owns its state independently.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize all three fields and subscribe upstream. |
| Source next rejected | Record source presence and advance the predicate index; emit nothing. |
| First matching next | Record the candidate; emit nothing yet. |
| Second matching next | Error with `SequenceError` immediately and close upstream; emit neither candidate. |
| Predicate throws | Forward the thrown error and close upstream. |
| Source complete, one match | Emit the candidate, then complete. |
| Source complete, no source values | Error with `EmptyError`. |
| Source complete, values but no match | Error with `NotFoundError`. |
| Source error | Forward the original error without publishing the candidate. |
| Downstream close | Close upstream and discard the candidate silently. |

## Time, cancellation, and sharing

- **Time:** No scheduler is introduced. Success waits for source completion; a second match fails at its arrival time, without waiting for completion.
- **Cancellation:** The first match keeps the source connected. A second match or predicate error closes it early. Downstream closure also releases it, without converting an unfinished validation into a value. Previously completed external effects remain completed.
- **Sharing:** No sharing or replay is introduced. Each connection validates only its observed sequence; a late connection to a shared source does not inspect earlier values.

## Worked trace

Illustrative cold source connected at `t = 0`, with predicate `value % 2 === 0`. Times are virtual milliseconds relative to connection.

| Time | Source event | Candidate / any value / next index | Output |
| --- | --- | --- | --- |
| 10 | `1` | `None / true / 1` | None |
| 20 | `4` | `Some(4) / true / 2` | None |
| 40 | `3` | `Some(4) / true / 3` | None |
| 50 | Complete | Released | `4`, then complete |

If the third value were another `4`, it would be a second match: `SequenceError` at `40`, no value output, and immediate source teardown.

## Verification obligations

- Reproduce the successful trace and second-match failure variant.
- Distinguish `EmptyError`, `NotFoundError`, and `SequenceError` by actual error type.
- Check predicate indexes and the supplied source reference.
- Preserve source/predicate exceptions without emitting a saved candidate.
- Check undefined payloads, optional-predicate behavior, and silent cancellation.

## Common confusion

`first(predicate)` accepts the first match and stops; `single(predicate)` must establish that no second match exists. `last(predicate)` accepts many matches and ultimately returns the latest. These choices express different requirements.

`single` has no default-value parameter. An empty source is an `EmptyError` even when a predicate is supplied; `NotFoundError` requires at least one source value and zero matches. Error notifications do not also produce completion.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/single)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/single.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
