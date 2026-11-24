# first

```text
Operator:       first
Stream shape:   T* → T?
State:          Nat — predicate index while searching
Initial state:  0
Value step:     (Nat × T) → (Nat × Action[]) — first match emits and completes
Ownership:      One state per operator subscription
Lifecycle:      First match closes upstream; empty matching result errors on completion; errors/cancel end the search
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** first(predicate), without a default value · **Format:** Detailed

> Emit the first value accepted by the predicate and immediately finish.

## Type shape

```text
first :: (T × Nat × T* → Bool) → T* → T?
```

The predicate receives the value, its zero-based source index, and the source Observable. `T?` bounds output `next` notifications to at most one. Success produces one value; error, cancellation, or an unfinished search may produce none. This card's main rule supplies a predicate and no default.

## Intuition

Readings arrive over time, and you need the first one meeting a requirement. Reject unsuitable readings, then deliver the first suitable reading immediately. Once selected, nothing later can change which match was first, so the operator finishes and releases its source connection. The predicate supplies the requirement; `first` supplies the selection and lifetime rules.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Evaluate the predicate; a rejection produces nothing, while the first acceptance emits the value and completes. |
| Whole connection | Exactly one result if a match arrives before error or downstream closure. Normal source completion without a match produces `EmptyError`. |

An empty source and a nonempty source whose values all fail the predicate have the same missing-match outcome. Finding a match does not require source completion. Conversely, a source that never matches and never terminates leaves the search unfinished.

## Remembers

`Nat` is the next zero-based index passed to the predicate. It advances for every examined source value, including rejected values. Each connection owns its index and source subscription; no earlier payloads need to be retained. `Action[]` describes ordered decisions: an accepted value is emitted before downstream completion. Overall closure, rather than a stored candidate, ends further searching.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize the index to zero and subscribe to the source. |
| Source next, predicate false | Advance the index; emit nothing. |
| Source next, predicate true | Emit that value, complete downstream, and close this upstream subscription. |
| Predicate throws | Forward the thrown error and close upstream. |
| Source complete before a match | Error downstream with `EmptyError`. |
| Source error before a match | Forward the original error; do not replace it with `EmptyError`. |
| Downstream close | End the search and close upstream silently. |

## Time, cancellation, and sharing

- **Time:** No scheduler or timer is added. The selected value is delivered during its source notification, followed immediately by completion. There is no built-in search deadline.
- **Cancellation:** Success ends the connection early; downstream closure also releases it. A cooperative synchronous source can stop producing immediately. Previously performed effects and independently shared work are not reversed.
- **Sharing:** No sharing boundary is introduced. Each connection searches independently from its own arrival point, even when the underlying source is shared.

## Worked trace

Illustrative cold source, connected at `t = 0`, with predicate `value % 2 === 0`. Times are virtual milliseconds from that connection. The source plans `1@10`, `4@20`, `6@40`, and completion at `50`.

| Time | Event | Predicate index | Output |
| --- | --- | --- | --- |
| 10 | Source `1` | `0` | None |
| 20 | Source `4` | `1` | `4`, then complete |
| 20 | Upstream teardown | — | Remaining source work cancelled |
| 40, 50 | Original source plan | — | No further delivery |

Completion and teardown follow the accepted value in the same virtual frame. The predicate is never called for the cancelled `6`.

## Verification obligations

- Reproduce the trace and early teardown, including predicate indexes and source argument.
- Check both empty and nonempty no-match sources for `EmptyError`.
- Preserve source errors and predicate exceptions without producing a default.
- Treat a matching `undefined` as a real value.
- Confirm downstream cancellation produces neither a result nor completion.

## Common confusion

`first()` with no predicate accepts the first source value. It differs from `take(1)` on an empty source: `first()` errors, while `take(1)` completes without a value.

Outside this card's no-default scope, a supplied **second argument** replaces only the missing-match outcome with one default value followed by completion. Explicitly supplying `undefined` still supplies a default. A default does not recover source or predicate errors and cannot make a nonterminating search finish.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/first)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/first.ts)
- [RxJS 7.8.2 take implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/take.ts)
- [RxJS 7.8.2 filter implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/filter.ts)
- [RxJS 7.8.2 throwIfEmpty implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/throwIfEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
