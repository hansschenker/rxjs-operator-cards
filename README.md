# RxJS Operator Cards

**A consistent, readable description of what each RxJS operator does to values and connections over time.**

Project direction: **Hans Schenker**. Main contributor and AI co-author of this first edition: **ChatGPT GPT-6 Astra Pro (OpenAI)**. See the [contributors list](CONTRIBUTORS.md).

The central idea is:

```text
Operator description = Type shape + Behavior rules
```

A compact Cω-inspired type tells us what goes in and what comes out. The behavior rules explain what happens when a value, timer, notifier, inner notification, completion, error, or connection closure arrives.

This repository preserves the operator-card idea developed in our discussion of Erik Meijer's talk on duality on **23 November 2026**. The reference implementation for the cards is **RxJS 7.8.2**.

## Start here

1. Read the [notation guide](docs/notation.md).
2. Open the [operator index](docs/operator-index.md) and compare related cards.
3. Use the [card template](templates/operator-card.md) to document another operator.
4. Read the [design](docs/operator-card-design.md) and [writing contract](docs/card-writing-contract.md) when extending the collection.
5. Follow the [eight-session plan](docs/session-plan.md) to complete the pinned operator inventory in batches of 15.

**Session 1 is complete: 15 worked cards.** The collection began with 14 cards, and `startWith` completes today's batch. The [session plan](docs/session-plan.md) assigns all **113 operator source files** from the audited RxJS 7.x directory: seven sessions of 15 and a final session of eight. **98 cards remain planned.** Session 2 is next, on **24 November 2026**.

The first six sessions cover 90 current operator files; the final two cover 23 deprecated forms. Four internal helpers are recorded separately. This is coverage of the pinned operators directory, including its aliases and compatibility forms; creation functions and other RxJS APIs have their own scope.

## Session 1 cards

| Family | Cards |
| --- | --- |
| Value transformation and selection | [map](cards/map.md), [filter](cards/filter.md) |
| Starting values | [startWith](cards/startWith.md) |
| Remembered state and aggregation | [scan](cards/scan.md), [reduce](cards/reduce.md) |
| Count and notifier boundaries | [take](cards/take.md), [takeUntil](cards/takeUntil.md) |
| Grouping | [buffer](cards/buffer.md), [window](cards/window.md) |
| Time | [debounceTime](cards/debounceTime.md) |
| Inner-stream coordination | [mergeMap](cards/mergeMap.md), [concatMap](cards/concatMap.md), [switchMap](cards/switchMap.md), [exhaustMap](cards/exhaustMap.md) |
| Sharing and replay | [shareReplay](cards/shareReplay.md) |

## What a card contains

Each card follows **Intuition → Specification → Verification**:

- **Type shape:** supplied functions or values, input stream, and output stream.
- **Local and whole-stream behavior:** what one event can produce, and what the entire connection can emit.
- **Remembers:** state retained between events and who owns it.
- **Event → action:** the rules for relevant source, inner, timer, and notifier events.
- **Time, cancellation, and sharing:** the clock, connection lifetime, and execution ownership.
- **Worked trace:** a small, explicit example of the resulting behavior.
- **Verification obligations:** observable properties that a review or test should check.
- **References:** official documentation and the pinned implementation.

Verification obligations describe what should be checked; concrete checks performed for this edition are recorded separately in the [validation report](docs/validation.md).

## A small example

```text
filter :: (T! → Bool!) → (T* → T*)

For one source value: T! → T?
For the whole stream: T* → T*

When a source value arrives:
  Forward it if the supplied predicate returns true.

When the source completes or errors:
  Forward that terminal notification.

When the downstream connection closes:
  Close the upstream connection.
```

The payload's business meaning belongs in the supplied predicate. The operator provides the forwarding rule. The [complete filter card](cards/filter.md) also states error, state, timing, and sharing behavior.

## Why type shape and behavior belong together

These operators share one broad type:

```text
(T! → R*) → (T* → R*)
```

Their policies differ:

| Operator | Policy |
| --- | --- |
| `mergeMap` | Allow overlap |
| `concatMap` | Queue until the current inner completes |
| `switchMap` | Close the previous inner and connect the newest |
| `exhaustMap` | Ignore new source values while an inner is active |

Likewise, `buffer` publishes arrays, while `window` publishes live inner streams:

```text
buffer :: U* → (T* → (T[])*)
window :: U* → (T* → (T*)*)
```

The notation preserves this distinction. It also preserves the difference between normal completion, failure, and consumer closure.

## Scope and conventions

- `T*` is shorthand for `Observable<T>` in stream positions. `T?` is an at-most-one stream, not `T | undefined`.
- `T!` describes one ordinary value in a local function signature. Whole-stream lower bounds require stated success/progress conditions.
- These are explanatory signatures, not replacement TypeScript declarations or a new implementation of RxJS.
- Historical Cω automatically flattened nested streams. This notation keeps `(T*)*` distinct from `T*`, because RxJS needs an explicit flattening policy.
- The simplified Pull, Push, and scheduler types are expanded with termination and closure where behavior requires them.
- Ordinary pipeable state is described per connection; shared sources and sharing boundaries are identified explicitly.

No build step is needed. The cards are Markdown documents readable directly on GitHub.

## Extending the collection

See [CONTRIBUTING.md](CONTRIBUTING.md), the [template](templates/operator-card.md), and the [session plan](docs/session-plan.md). The [session manifest](planning/operator-sessions.json) records assigned and completed operators; the [source inventory](planning/source-inventory.json) pins the upstream files. Keep teaching language accessible and support precise claims with source references and traces.

## Credits and sources

**Hans Schenker** defined the teaching direction and requested a reusable operator-card collection. **ChatGPT GPT-6 Astra Pro (OpenAI)** is the main contributor and AI co-author of the initial specification, notation guide, worked cards, and behavioral review.

The work draws on Erik Meijer's explanations of duality and Cω, and on the RxJS project's documentation and source. These are intellectual sources; their authors are not being represented as contributors to or endorsers of this repository. See [references](docs/references.md) and [contributors](CONTRIBUTORS.md).
