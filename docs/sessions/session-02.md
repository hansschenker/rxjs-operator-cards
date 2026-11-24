# Session 2 — Selection, boundaries, and distinct values

**Date:** 24 November 2026 · **Reference:** RxJS 7.8.2 · **Batch:** 15 operators

Session 2 expands the existing compact cards into detailed explanations. Each card retains its seven-field overview and adds a function-first type, practical intuition, local and whole-stream rules, remembered state, event/action table, explicit time/cancellation/sharing, a worked trace, verification obligations, and primary references.

## The 15 cards

| Topic | Cards |
| --- | --- |
| Count boundaries and trailing values | [skip](../../cards/skip.md), [takeLast](../../cards/takeLast.md), [skipLast](../../cards/skipLast.md) |
| Predicate and notifier boundaries | [takeWhile](../../cards/takeWhile.md), [skipWhile](../../cards/skipWhile.md), [skipUntil](../../cards/skipUntil.md) |
| Selection with a required result | [first](../../cards/first.md), [last](../../cards/last.md), [single](../../cards/single.md), [elementAt](../../cards/elementAt.md) |
| Searching with an absence value | [find](../../cards/find.md), [findIndex](../../cards/findIndex.md) |
| Remembering distinct values | [distinct](../../cards/distinct.md), [distinctUntilChanged](../../cards/distinctUntilChanged.md), [distinctUntilKeyChanged](../../cards/distinctUntilKeyChanged.md) |

The following comparisons were executed against RxJS 7.8.2. They use cold sources with independent subscriptions and virtual time. Numbers after `@` are milliseconds after connection. Source completion occurs at 50; teardown cancels outstanding source events. Error and completion are terminal notifications, not payload values.

## 1. The same surviving values can arrive at different times

Source: `1@10`, `2@20`, `3@30`, `4@40`, complete at 50.

| Operator | Values delivered | Output termination |
| --- | --- | --- |
| `take(2)` | `1@10`, `2@20` | Complete at 20; close upstream early |
| `skip(2)` | `3@30`, `4@40` | Complete at 50 |
| `takeLast(2)` | `3@50`, `4@50` | Complete at 50 after both values |
| `skipLast(2)` | `1@30`, `2@40` | Complete at 50; discard the retained tail |

`skip` counts an initial prefix. `takeLast` must wait for normal completion to know the final suffix. `skipLast` can release an older value when enough newer values have arrived. The broad `T* → T*` shape leaves these rules open.

## 2. A predicate can select values or define a boundary

Source: `1@10`, `2@20`, `3@30`, `1@40`, complete at 50. Predicate: values smaller than 3. Use the default non-inclusive `takeWhile` scope.

| Operator | Values delivered | Behavior after the first rejection |
| --- | --- | --- |
| `filter(predicate)` | `1@10`, `2@20`, `1@40` | Continue checking later values |
| `takeWhile(predicate)` | `1@10`, `2@20` | Complete at 30 and close upstream |
| `skipWhile(predicate)` | `3@30`, `1@40` | Forward from that value onward; stop calling the predicate |

The final `1` makes the difference visible. A condition becoming true again does not reopen a completed `takeWhile` connection or restore `skipWhile`'s initial skipping phase.

## 3. First, last, and exactly one are different requests

Source: `1@10`, `2@20`, `3@30`, `4@40`, complete at 50. Predicate: even values. No defaults are supplied.

| Operator | Result | Why this event decides it |
| --- | --- | --- |
| `first(predicate)` | `2@20`, then complete | The first matching value is sufficient |
| `last(predicate)` | `4@50`, then complete | Normal completion establishes which match was last |
| `single(predicate)` | `SequenceError@40`; no result value | The second match violates the exactly-one requirement |
| `find(predicate)` | `2@20`, then complete | The first match resolves the search |
| `findIndex(predicate)` | `1@20`, then complete | The first match has zero-based source index 1 |

`single` retains its first match privately. It cannot publish a successful result until normal completion establishes that no second match exists.

## 4. Absence is part of the operator's contract

The source completes normally. For the nonempty case it contains `1, 3`; the predicate still asks for even values. The `first` and `last` calls have no default argument.

| Operator | Nonempty source, no match | Empty source |
| --- | --- | --- |
| `first(predicate)` | `EmptyError` | `EmptyError` |
| `last(predicate)` | `EmptyError` | `EmptyError` |
| `single(predicate)` | `NotFoundError` | `EmptyError` |
| `find(predicate)` | Emit `undefined`, then complete | Emit `undefined`, then complete |
| `findIndex(predicate)` | Emit `-1`, then complete | Emit `-1`, then complete |

The `undefined` produced by `find` is an actual `next` payload. It is different from `None` in a compact value step, where `None` means that step emits nothing. The result shape `(T | Undefined)?` keeps the payload alternative separate from the at-most-one emission bound.

## 5. Distinctness depends on the remembered history

Source: `1@10`, `1@20`, `2@30`, `1@40`, complete at 50. Use the default comparisons and no flush notifier.

| Operator | Values delivered | Memory relevant to the decision |
| --- | --- | --- |
| `distinct()` | `1@10`, `2@30` | All keys admitted so far |
| `distinctUntilChanged()` | `1@10`, `2@30`, `1@40` | The last accepted value/key |

The individual cards also make equality and reference ownership explicit. `distinct` uses Set membership; `distinctUntilChanged` uses `===` by default; `distinctUntilKeyChanged` retains the last emitted object reference and reads the selected property for comparisons. Those differences belong in the state model, alongside the stream shape.

## Review focus

- Keep a rejected payload separate from a closed connection.
- State whether a result is available immediately or requires normal completion.
- Name what happens for an empty source, a nonempty source without a match, and an error.
- Identify whether memory contains a counter, gate, trailing buffer, candidate, key set, selected key, or object reference.
- Keep state per operator connection unless an explicit sharing boundary changes ownership.
- Show cancellation as resource closure; it does not manufacture a successful result or completion notification.

The [validation record](../validation.md#session-2-detailed-expansion--24-november-2026) records the executed scenarios and their limits. The [operator index](../operator-index.md) and [session plan](../session-plan.md#session-2) provide navigation and progress.
