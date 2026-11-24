# Operator-card session plan

**Plan date:** 23 November 2026 · **Timezone:** Europe/Zurich · **Card reference:** RxJS 7.8.2

Maintain **one card per operator source file**, grouped by related behavior into sessions of **15 operators**. All **113 operator files** now have compact notation. The **seven sessions of 15 and one final session of eight** continue to organize detailed expansion.

**Compact notation: complete for all 113 operators. Detailed expansion: Session 1 complete; Session 2 is next.**

Session 1 established 15 detailed cards on 23 November 2026, including [startWith](../cards/startWith.md). On 24 November 2026, the requested compact-notation pass added the remaining 98 cards. The collection now has **113 compact overviews, with 15 detailed explanations and 98 detailed expansions planned**.

## Inventory and counting rules

The [requested 7.x directory](https://github.com/ReactiveX/rxjs/tree/7.x/src/internal/operators) was inspected at commit [e5351d0](https://github.com/ReactiveX/rxjs/tree/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators). A moving branch can change; this plan uses that fixed snapshot.

| Inventory | Count |
| --- | ---: |
| TypeScript files in the directory | 117 |
| Operator source files | 113 |
| Current operator source files | 90 |
| Deprecated operator source files | 23 |
| Internal helpers, recorded separately | 4 |
| Public operator names across both entry points | 114 |

All **117 source-file Git blob hashes match the installed RxJS 7.8.2 sources** used for the cards. This comparison concerns the requested directory; it does not claim that the entire branch and release are identical.

The four helpers are `OperatorSubscriber.ts`, `joinAllInternals.ts`, `mergeInternals.ts`, and `scanInternals.ts`. They support implementation explanations and are not assigned operator-card slots.

There is one extra public name: the deprecated pipeable `onErrorResumeNext` export from `rxjs/operators` aliases `onErrorResumeNextWith`. Its separate compact alias block appears in the `onErrorResumeNextWith` card in Session 6. That gives coverage of **113 source files and 114 public names** without inventing an extra implementation.

The deprecated files `combineAll`, `exhaust`, and `flatMap` are aliases too, but each has its own source file and its own discoverable card entry.

## Schedule

The dates below preserve the original proposed cadence of **one session per calendar day**, including weekends. They now apply to detailed expansion of existing compact cards. They are planning targets, not scheduled automation; progress is determined by reviewed content rather than a date passing.

| Session | Original target date | Focus | Operators | Cumulative target | Detailed status |
| --- | --- | --- | ---: | ---: | --- |
| [1](#session-1) | 23 November 2026 | Core behavior and state | 15 | 15 | Complete |
| [2](#session-2) | 24 November 2026 | Selection, boundaries, and distinct values | 15 | 30 | Planned |
| [3](#session-3) | 25 November 2026 | Time, scheduling, and delivery boundaries | 15 | 45 | Planned |
| [4](#session-4) | 26 November 2026 | Grouping, aggregation, and sequence comparison | 15 | 60 | Planned |
| [5](#session-5) | 27 November 2026 | Combining, flattening, and expanding streams | 15 | 75 | Planned |
| [6](#session-6) | 28 November 2026 | Completion, recovery, effects, and shared execution | 15 | 90 | Planned |
| [7](#session-7) | 29 November 2026 | Compatibility: aliases and older operator forms | 15 | 105 | Planned |
| [8](#session-8) | 30 November 2026 | Compatibility: multicast and resubscription | 8 | 113 | Planned |

Sessions 1–6 cover the **90 current operator files**. Sessions 7–8 cover all **23 deprecated operator files**. The last session expands eight compact cards, followed by a review of the detailed collection. Compact coverage is already complete in every session.

## Session batches

Every name below links to its pinned source and existing card. **Detailed** marks the original full explanations; **Compact** marks a completed seven-field notation card whose detailed expansion remains planned. Both formats state their scope and reference RxJS 7.8.2.

## Session 1

**23 November 2026 — Core behavior and state**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Complete

Establish the card vocabulary: one-value rules, remembered state, completion, nested streams, flattening policies, sharing, and explicit starting values.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`map`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/map.ts) | [Detailed](../cards/map.md) |
| 2 | [`filter`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/filter.ts) | [Detailed](../cards/filter.md) |
| 3 | [`scan`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/scan.ts) | [Detailed](../cards/scan.md) |
| 4 | [`reduce`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/reduce.ts) | [Detailed](../cards/reduce.md) |
| 5 | [`take`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/take.ts) | [Detailed](../cards/take.md) |
| 6 | [`takeUntil`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/takeUntil.ts) | [Detailed](../cards/takeUntil.md) |
| 7 | [`buffer`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/buffer.ts) | [Detailed](../cards/buffer.md) |
| 8 | [`window`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/window.ts) | [Detailed](../cards/window.md) |
| 9 | [`debounceTime`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/debounceTime.ts) | [Detailed](../cards/debounceTime.md) |
| 10 | [`mergeMap`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/mergeMap.ts) | [Detailed](../cards/mergeMap.md) |
| 11 | [`concatMap`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/concatMap.ts) | [Detailed](../cards/concatMap.md) |
| 12 | [`switchMap`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/switchMap.ts) | [Detailed](../cards/switchMap.md) |
| 13 | [`exhaustMap`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/exhaustMap.ts) | [Detailed](../cards/exhaustMap.md) |
| 14 | [`shareReplay`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/shareReplay.ts) | [Detailed](../cards/shareReplay.md) |
| 15 | [`startWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/startWith.ts) | [Detailed](../cards/startWith.md) |

## Session 2

**24 November 2026 — Selection, boundaries, and distinct values**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Planned

Distinguish passing, skipping, and ending a stream; compare first/last/single-result requirements and the history remembered by distinct operators.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`skip`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/skip.ts) | [Compact](../cards/skip.md) |
| 2 | [`takeLast`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/takeLast.ts) | [Compact](../cards/takeLast.md) |
| 3 | [`skipLast`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/skipLast.ts) | [Compact](../cards/skipLast.md) |
| 4 | [`takeWhile`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/takeWhile.ts) | [Compact](../cards/takeWhile.md) |
| 5 | [`skipWhile`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/skipWhile.ts) | [Compact](../cards/skipWhile.md) |
| 6 | [`skipUntil`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/skipUntil.ts) | [Compact](../cards/skipUntil.md) |
| 7 | [`first`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/first.ts) | [Compact](../cards/first.md) |
| 8 | [`last`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/last.ts) | [Compact](../cards/last.md) |
| 9 | [`single`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/single.ts) | [Compact](../cards/single.md) |
| 10 | [`elementAt`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/elementAt.ts) | [Compact](../cards/elementAt.md) |
| 11 | [`find`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/find.ts) | [Compact](../cards/find.md) |
| 12 | [`findIndex`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/findIndex.ts) | [Compact](../cards/findIndex.md) |
| 13 | [`distinct`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/distinct.ts) | [Compact](../cards/distinct.md) |
| 14 | [`distinctUntilChanged`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/distinctUntilChanged.ts) | [Compact](../cards/distinctUntilChanged.md) |
| 15 | [`distinctUntilKeyChanged`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/distinctUntilKeyChanged.ts) | [Compact](../cards/distinctUntilKeyChanged.md) |

The main comparisons for Session 2 are `takeWhile`/ `skipWhile`, `takeUntil`/ `skipUntil`, `first`/ `last`/ `single`, and the three `distinct` forms. Use the existing compact cards as the starting point and expand their explanation and verification sections.

## Session 3

**25 November 2026 — Time, scheduling, and delivery boundaries**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Planned

Identify the source, notifier, clock, or completion event that controls delivery. Compare connection scheduling with notification scheduling. `endWith` is the completion-triggered boundary example, not a clock-based operator.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`debounce`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/debounce.ts) | [Compact](../cards/debounce.md) |
| 2 | [`throttle`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/throttle.ts) | [Compact](../cards/throttle.md) |
| 3 | [`throttleTime`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/throttleTime.ts) | [Compact](../cards/throttleTime.md) |
| 4 | [`audit`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/audit.ts) | [Compact](../cards/audit.md) |
| 5 | [`auditTime`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/auditTime.ts) | [Compact](../cards/auditTime.md) |
| 6 | [`sample`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/sample.ts) | [Compact](../cards/sample.md) |
| 7 | [`sampleTime`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/sampleTime.ts) | [Compact](../cards/sampleTime.md) |
| 8 | [`delay`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/delay.ts) | [Compact](../cards/delay.md) |
| 9 | [`delayWhen`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/delayWhen.ts) | [Compact](../cards/delayWhen.md) |
| 10 | [`timeout`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/timeout.ts) | [Compact](../cards/timeout.md) |
| 11 | [`timestamp`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/timestamp.ts) | [Compact](../cards/timestamp.md) |
| 12 | [`timeInterval`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/timeInterval.ts) | [Compact](../cards/timeInterval.md) |
| 13 | [`observeOn`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/observeOn.ts) | [Compact](../cards/observeOn.md) |
| 14 | [`subscribeOn`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/subscribeOn.ts) | [Compact](../cards/subscribeOn.md) |
| 15 | [`endWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/endWith.ts) | [Compact](../cards/endWith.md) |

## Session 4

**26 November 2026 — Grouping, aggregation, and sequence comparison**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Planned

Compare array batches with live windows, then inspect grouping keys, remembered neighbors, final aggregates, and whole-sequence comparison.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`bufferCount`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/bufferCount.ts) | [Compact](../cards/bufferCount.md) |
| 2 | [`windowCount`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/windowCount.ts) | [Compact](../cards/windowCount.md) |
| 3 | [`bufferTime`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/bufferTime.ts) | [Compact](../cards/bufferTime.md) |
| 4 | [`windowTime`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/windowTime.ts) | [Compact](../cards/windowTime.md) |
| 5 | [`bufferToggle`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/bufferToggle.ts) | [Compact](../cards/bufferToggle.md) |
| 6 | [`windowToggle`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/windowToggle.ts) | [Compact](../cards/windowToggle.md) |
| 7 | [`bufferWhen`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/bufferWhen.ts) | [Compact](../cards/bufferWhen.md) |
| 8 | [`windowWhen`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/windowWhen.ts) | [Compact](../cards/windowWhen.md) |
| 9 | [`groupBy`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/groupBy.ts) | [Compact](../cards/groupBy.md) |
| 10 | [`pairwise`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/pairwise.ts) | [Compact](../cards/pairwise.md) |
| 11 | [`toArray`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/toArray.ts) | [Compact](../cards/toArray.md) |
| 12 | [`count`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/count.ts) | [Compact](../cards/count.md) |
| 13 | [`min`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/min.ts) | [Compact](../cards/min.md) |
| 14 | [`max`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/max.ts) | [Compact](../cards/max.md) |
| 15 | [`sequenceEqual`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/sequenceEqual.ts) | [Compact](../cards/sequenceEqual.md) |

## Session 5

**27 November 2026 — Combining, flattening, and expanding streams**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Planned

Compare overlap, serial admission, replacement, busy gating, pairing, latest-value combination, accumulated inner state, and recursive expansion.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`mergeAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/mergeAll.ts) | [Compact](../cards/mergeAll.md) |
| 2 | [`concatAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/concatAll.ts) | [Compact](../cards/concatAll.md) |
| 3 | [`switchAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/switchAll.ts) | [Compact](../cards/switchAll.md) |
| 4 | [`exhaustAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/exhaustAll.ts) | [Compact](../cards/exhaustAll.md) |
| 5 | [`mergeWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/mergeWith.ts) | [Compact](../cards/mergeWith.md) |
| 6 | [`concatWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/concatWith.ts) | [Compact](../cards/concatWith.md) |
| 7 | [`combineLatestWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/combineLatestWith.ts) | [Compact](../cards/combineLatestWith.md) |
| 8 | [`zipWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/zipWith.ts) | [Compact](../cards/zipWith.md) |
| 9 | [`withLatestFrom`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/withLatestFrom.ts) | [Compact](../cards/withLatestFrom.md) |
| 10 | [`raceWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/raceWith.ts) | [Compact](../cards/raceWith.md) |
| 11 | [`combineLatestAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/combineLatestAll.ts) | [Compact](../cards/combineLatestAll.md) |
| 12 | [`zipAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/zipAll.ts) | [Compact](../cards/zipAll.md) |
| 13 | [`mergeScan`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/mergeScan.ts) | [Compact](../cards/mergeScan.md) |
| 14 | [`switchScan`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/switchScan.ts) | [Compact](../cards/switchScan.md) |
| 15 | [`expand`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/expand.ts) | [Compact](../cards/expand.md) |

## Session 6

**28 November 2026 — Completion, recovery, effects, and shared execution**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Planned

Keep next, error, complete, and consumer closure separate. Study completion decisions, recovery/resubscription, effects, cleanup, and the ownership of shared execution.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`every`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/every.ts) | [Compact](../cards/every.md) |
| 2 | [`isEmpty`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/isEmpty.ts) | [Compact](../cards/isEmpty.md) |
| 3 | [`defaultIfEmpty`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/defaultIfEmpty.ts) | [Compact](../cards/defaultIfEmpty.md) |
| 4 | [`throwIfEmpty`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/throwIfEmpty.ts) | [Compact](../cards/throwIfEmpty.md) |
| 5 | [`ignoreElements`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/ignoreElements.ts) | [Compact](../cards/ignoreElements.md) |
| 6 | [`materialize`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/materialize.ts) | [Compact](../cards/materialize.md) |
| 7 | [`dematerialize`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/dematerialize.ts) | [Compact](../cards/dematerialize.md) |
| 8 | [`catchError`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/catchError.ts) | [Compact](../cards/catchError.md) |
| 9 | [`onErrorResumeNextWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/onErrorResumeNextWith.ts) | [Compact](../cards/onErrorResumeNextWith.md) |
| 10 | [`retry`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/retry.ts) | [Compact](../cards/retry.md) |
| 11 | [`repeat`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/repeat.ts) | [Compact](../cards/repeat.md) |
| 12 | [`tap`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/tap.ts) | [Compact](../cards/tap.md) |
| 13 | [`finalize`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/finalize.ts) | [Compact](../cards/finalize.md) |
| 14 | [`share`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/share.ts) | [Compact](../cards/share.md) |
| 15 | [`connect`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/connect.ts) | [Compact](../cards/connect.md) |

The `onErrorResumeNextWith` card includes the deprecated pipeable alias `onErrorResumeNext` from `rxjs/operators`; retain its separate notation block when expanding the card. The root `rxjs` creation function with that old spelling is a different API.

## Session 7

**29 November 2026 — Compatibility: aliases and older operator forms**  
**Batch:** 15 operators · **Compact:** Complete · **Detailed:** Planned

Make older names discoverable and explain their current alternatives. State import paths and preserve behavioral details when describing a migration.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`mapTo`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/mapTo.ts) | [Compact](../cards/mapTo.md) |
| 2 | [`pluck`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/pluck.ts) | [Compact](../cards/pluck.md) |
| 3 | [`flatMap`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/flatMap.ts) | [Compact](../cards/flatMap.md) |
| 4 | [`concatMapTo`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/concatMapTo.ts) | [Compact](../cards/concatMapTo.md) |
| 5 | [`mergeMapTo`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/mergeMapTo.ts) | [Compact](../cards/mergeMapTo.md) |
| 6 | [`switchMapTo`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/switchMapTo.ts) | [Compact](../cards/switchMapTo.md) |
| 7 | [`combineAll`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/combineAll.ts) | [Compact](../cards/combineAll.md) |
| 8 | [`exhaust`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/exhaust.ts) | [Compact](../cards/exhaust.md) |
| 9 | [`combineLatest`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/combineLatest.ts) | [Compact](../cards/combineLatest.md) |
| 10 | [`concat`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/concat.ts) | [Compact](../cards/concat.md) |
| 11 | [`merge`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/merge.ts) | [Compact](../cards/merge.md) |
| 12 | [`race`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/race.ts) | [Compact](../cards/race.md) |
| 13 | [`zip`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/zip.ts) | [Compact](../cards/zip.md) |
| 14 | [`partition`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/partition.ts) | [Compact](../cards/partition.md) |
| 15 | [`timeoutWith`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/timeoutWith.ts) | [Compact](../cards/timeoutWith.md) |

Here `combineLatest`, `concat`, `merge`, `race`, `zip`, and `partition` refer to the **pipeable exports from `rxjs/operators`** in the requested directory. Their similarly named creation functions from `rxjs` are outside this inventory. Do not label those creation functions deprecated merely because these pipeable forms are deprecated.

## Session 8

**30 November 2026 — Compatibility: multicast and resubscription**  
**Batch:** 8 operators · **Compact:** Complete · **Detailed:** Planned

Explain the older multicast/replay/ref-count and notifier-driven resubscription policies. Finish with an inventory and cross-card consistency review.

| Position | Operator source | Card |
| --- | --- | --- |
| 1 | [`multicast`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/multicast.ts) | [Compact](../cards/multicast.md) |
| 2 | [`publish`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/publish.ts) | [Compact](../cards/publish.md) |
| 3 | [`publishBehavior`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/publishBehavior.ts) | [Compact](../cards/publishBehavior.md) |
| 4 | [`publishLast`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/publishLast.ts) | [Compact](../cards/publishLast.md) |
| 5 | [`publishReplay`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/publishReplay.ts) | [Compact](../cards/publishReplay.md) |
| 6 | [`refCount`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/refCount.ts) | [Compact](../cards/refCount.md) |
| 7 | [`repeatWhen`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/repeatWhen.ts) | [Compact](../cards/repeatWhen.md) |
| 8 | [`retryWhen`](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/retryWhen.ts) | [Compact](../cards/retryWhen.md) |

## Reading guidance

Keep corresponding buffer/window and take/skip rules beside each other. Learn the ordinary combination operators before their `All` forms. The batch size is consistent, but the review effort varies: grouping, higher-order state, and sharing usually require more cases than a renamed alias.

| Later topic | Reuse these earlier cards |
| --- | --- |
| `mergeScan`, `switchScan` | `scan` and the corresponding flattening policy |
| `expand` | `mergeMap`, then add the recursive-generation rule |
| `sequenceEqual` | `reduce` and two-source completion/coordination reasoning |
| `connect` | `share`, then identify the selector's connection ownership |
| `repeatWhen`, `retryWhen` | `repeat`, `retry`, and explicit notifier next/complete/error rules |

Compatibility cards must explain the specific modern alternative and the conditions needed to preserve behavior. `partition` needs an adapted two-output shape; `refCount` needs its connectable-source context; publishing variants need their subject, replay, and reset policies. A compatibility label does not make these all simple aliases.

## How each session runs

1. **Read current progress.** Open this plan, the [machine-readable session manifest](../planning/operator-sessions.json), and the operator index. Continue unfinished work before adding new names; do not recreate accepted cards.
2. **Inspect the reference.** Read each operator's pinned RxJS 7.8.2 implementation and relevant helpers. Name the overload, scheduler, notifier, and sharing policy being described.
3. **Expand the card.** Preserve its compact overview and source-reviewed notes, then follow [Intuition → Specification → Verification](card-writing-contract.md) and the [ten-section template](../templates/operator-card.md). Keep Cω shape, local/global cardinality, time, cancellation, and sharing explicit.
4. **Check distinguishing behavior.** Use worked traces and focused checks that separate the operator from its neighbors. Count an unexecuted verification obligation as future work, never as a passed test.
5. **Review the batch.** Check notation, lifecycle rules, source references, links, and comparisons. A 15-card quota does not remove the need for an explicit scope.
6. **Save and update progress.** Update the card's format in the index, add reviewed detailed cards to `completedOperators`, update detailed session status, record validation, and commit with the project's contributor attribution. Keep `compactOperators` as the separate record of existing notation coverage.

The planning files are an inventory and progress record. They do not implement an automatic card generator.

### Completion criteria

A compact card is complete when its seven fields, scope, local notation, lifecycle/ownership notes, and primary references have been reviewed. All 113 source-file cards meet this compact-format checkpoint. This does not assert complete overload coverage or a formal equivalence proof.

A detailed card is complete when its declared scope is documented, its illustrative trace and distinguishing behavior have been reviewed, relevant checks are recorded honestly, and its links and required sections are valid. An operator card can identify additional overloads as future variants; card completion does not assert exhaustive proof of every overload and execution.

A session's detailed status is complete when all its assigned cards meet the detailed criteria. If work remains, mark it in progress and carry it forward. Recompute later target dates as needed instead of silently marking incomplete work done.

### Resume commands

- **Next:** “Expand operator-card Session 2 into detailed cards.”
- **Later:** “Continue the next detailed operator-card session.”

Read repository progress when resuming; the manifest is more reliable than a remembered session number.

## Plan verification

- All 113 operator source files are assigned exactly once.
- Sessions 1–7 contain 15 names each; Session 8 contains eight.
- The four helper files have no operator-card assignment.
- The extra pipeable alias has an explicit required home in Session 6.
- All 113 operator cards have compact notation; the extra public alias also has its own seven-field block.
- Only the 15 detailed cards from Session 1 appear in `completedOperators`; the 98 compact-only cards are not marked as detailed expansions.
- Current and deprecated operator-file counts are 90 and 23.
- Deprecated overloads of current operators do not move the whole operator into a compatibility session.

The source snapshot and its blob hashes are stored in [source-inventory.json](../planning/source-inventory.json). Progress is stored in [operator-sessions.json](../planning/operator-sessions.json).

## Primary references

- [Requested 7.x operator directory](https://github.com/ReactiveX/rxjs/tree/7.x/src/internal/operators)
- [Pinned operator directory](https://github.com/ReactiveX/rxjs/tree/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators)
- [Pinned rxjs/operators export index](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/operators/index.ts)
- [Pinned root rxjs export index](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/index.ts)
- [Pinned onErrorResumeNextWith and its alias](https://github.com/ReactiveX/rxjs/blob/e5351d02e225e275ac0e497c7b66eaa5f0c88791/src/internal/operators/onErrorResumeNextWith.ts)
- [RxJS 7.8.2 operator directory](https://github.com/ReactiveX/rxjs/tree/7.8.2/src/internal/operators)
