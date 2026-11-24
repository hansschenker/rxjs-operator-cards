# Operator index

**Reference:** RxJS 7.8.2 · **Compact notation:** 113 source-file cards / 114 public names · **Detailed cards:** 30

Every inventoried operator now has the same seven-field compact overview. The 30 **Detailed** cards add the full explanation, event/action table, worked trace, and verification obligations. The remaining 83 **Compact** cards provide a scoped overview, local notation and behavior notes, and pinned source references.

Stream shapes below are copied from each card’s opening block after its stated arguments and policy have been chosen. They describe explanatory dataflow, not every TypeScript overload. `T?` remains an Observable with at most one emitted value; it is not a scalar or an eventual-emission guarantee. Read each card’s scope, ownership, and lifecycle together.

The groups follow the [session plan](session-plan.md). Sessions 1–6 contain 90 current operator files; Sessions 7–8 contain 23 deprecated forms. Four internal helpers and creation functions are outside this card inventory.

## Core behavior and state

**Session 1:** 15 cards · Compact notation complete

| Operator | Stream shape | Format |
| --- | --- | --- |
| [map](../cards/map.md) | `T* → R*` | Detailed |
| [filter](../cards/filter.md) | `T* → T*` | Detailed |
| [scan](../cards/scan.md) | `T* → R*` | Detailed |
| [reduce](../cards/reduce.md) | `T* → R?` | Detailed |
| [take](../cards/take.md) | `T* → T* (at most n values)` | Detailed |
| [takeUntil](../cards/takeUntil.md) | `T* → T*` | Detailed |
| [buffer](../cards/buffer.md) | `T* → (T[])*` | Detailed |
| [window](../cards/window.md) | `T* → (T*)*` | Detailed |
| [debounceTime](../cards/debounceTime.md) | `T* → T*` | Detailed |
| [mergeMap](../cards/mergeMap.md) | `T* → R*` | Detailed |
| [concatMap](../cards/concatMap.md) | `T* → R*` | Detailed |
| [switchMap](../cards/switchMap.md) | `T* → R*` | Detailed |
| [exhaustMap](../cards/exhaustMap.md) | `T* → R*` | Detailed |
| [shareReplay](../cards/shareReplay.md) | `T* → T*` | Detailed |
| [startWith](../cards/startWith.md) | `T* → (P \| T)*` | Detailed |

## Selection, boundaries, and distinct values

**Session 2:** 15 cards · Compact notation and detailed expansion complete

See the [Session 2 comparison guide](sessions/session-02.md) for side-by-side traces and result-selection outcomes.

| Operator | Stream shape | Format |
| --- | --- | --- |
| [skip](../cards/skip.md) | `T* → T*` | Detailed |
| [takeLast](../cards/takeLast.md) | `T* → T* (at most n values)` | Detailed |
| [skipLast](../cards/skipLast.md) | `T* → T*` | Detailed |
| [takeWhile](../cards/takeWhile.md) | `T* → T*` | Detailed |
| [skipWhile](../cards/skipWhile.md) | `T* → T*` | Detailed |
| [skipUntil](../cards/skipUntil.md) | `T* → T*` | Detailed |
| [first](../cards/first.md) | `T* → T?` | Detailed |
| [last](../cards/last.md) | `T* → T?` | Detailed |
| [single](../cards/single.md) | `T* → T?` | Detailed |
| [elementAt](../cards/elementAt.md) | `T* → T?` | Detailed |
| [find](../cards/find.md) | `T* → (T \| Undefined)?` | Detailed |
| [findIndex](../cards/findIndex.md) | `T* → (Nat \| -1)?` | Detailed |
| [distinct](../cards/distinct.md) | `T* → T*` | Detailed |
| [distinctUntilChanged](../cards/distinctUntilChanged.md) | `T* → T*` | Detailed |
| [distinctUntilKeyChanged](../cards/distinctUntilKeyChanged.md) | `T* → T*` | Detailed |

## Time, scheduling, and delivery boundaries

**Session 3:** 15 cards · Compact notation complete

| Operator | Stream shape | Format |
| --- | --- | --- |
| [debounce](../cards/debounce.md) | `T* → T*` | Compact |
| [throttle](../cards/throttle.md) | `T* → T*` | Compact |
| [throttleTime](../cards/throttleTime.md) | `T* → T*` | Compact |
| [audit](../cards/audit.md) | `T* → T*` | Compact |
| [auditTime](../cards/auditTime.md) | `T* → T*` | Compact |
| [sample](../cards/sample.md) | `T* → T*` | Compact |
| [sampleTime](../cards/sampleTime.md) | `T* → T*` | Compact |
| [delay](../cards/delay.md) | `T* → T*` | Compact |
| [delayWhen](../cards/delayWhen.md) | `T* → T*` | Compact |
| [timeout](../cards/timeout.md) | `T* → T*` | Compact |
| [timestamp](../cards/timestamp.md) | `T* → Timed<T>*` | Compact |
| [timeInterval](../cards/timeInterval.md) | `T* → Interval<T>*` | Compact |
| [observeOn](../cards/observeOn.md) | `T* → T*` | Compact |
| [subscribeOn](../cards/subscribeOn.md) | `T* → T*` | Compact |
| [endWith](../cards/endWith.md) | `T* → (T \| P)*` | Compact |

## Grouping, aggregation, and sequence comparison

**Session 4:** 15 cards · Compact notation complete

| Operator | Stream shape | Format |
| --- | --- | --- |
| [bufferCount](../cards/bufferCount.md) | `T* → (T[])*` | Compact |
| [windowCount](../cards/windowCount.md) | `T* → (T*)*` | Compact |
| [bufferTime](../cards/bufferTime.md) | `T* → (T[])*` | Compact |
| [windowTime](../cards/windowTime.md) | `T* → (T*)*` | Compact |
| [bufferToggle](../cards/bufferToggle.md) | `T* → (T[])*` | Compact |
| [windowToggle](../cards/windowToggle.md) | `T* → (T*)*` | Compact |
| [bufferWhen](../cards/bufferWhen.md) | `T* → (T[])*` | Compact |
| [windowWhen](../cards/windowWhen.md) | `T* → (T*)*` | Compact |
| [groupBy](../cards/groupBy.md) | `T* → Group<K, T>*` | Compact |
| [pairwise](../cards/pairwise.md) | `T* → (T × T)*` | Compact |
| [toArray](../cards/toArray.md) | `T* → (T[])?` | Compact |
| [count](../cards/count.md) | `T* → Nat?` | Compact |
| [min](../cards/min.md) | `T* → T?` | Compact |
| [max](../cards/max.md) | `T* → T?` | Compact |
| [sequenceEqual](../cards/sequenceEqual.md) | `T* → Bool? (compareTo: T* supplied)` | Compact |

## Combining, flattening, and expanding streams

**Session 5:** 15 cards · Compact notation complete

| Operator | Stream shape | Format |
| --- | --- | --- |
| [mergeAll](../cards/mergeAll.md) | `(T*)* → T*` | Compact |
| [concatAll](../cards/concatAll.md) | `(T*)* → T*` | Compact |
| [switchAll](../cards/switchAll.md) | `(T*)* → T*` | Compact |
| [exhaustAll](../cards/exhaustAll.md) | `(T*)* → T*` | Compact |
| [mergeWith](../cards/mergeWith.md) | `A* → (A \| B)*` | Compact |
| [concatWith](../cards/concatWith.md) | `A* → (A \| B)*` | Compact |
| [combineLatestWith](../cards/combineLatestWith.md) | `A* → (A × B)*` | Compact |
| [zipWith](../cards/zipWith.md) | `A* → (A × B)*` | Compact |
| [withLatestFrom](../cards/withLatestFrom.md) | `A* → (A × B)*` | Compact |
| [raceWith](../cards/raceWith.md) | `A* → (A \| B)*` | Compact |
| [combineLatestAll](../cards/combineLatestAll.md) | `(T*)* → (T[])*` | Compact |
| [zipAll](../cards/zipAll.md) | `(T*)* → (T[])*` | Compact |
| [mergeScan](../cards/mergeScan.md) | `T* → R*` | Compact |
| [switchScan](../cards/switchScan.md) | `T* → R*` | Compact |
| [expand](../cards/expand.md) | `T* → T*` | Compact |

## Completion, recovery, effects, and shared execution

**Session 6:** 15 cards · Compact notation complete

| Operator | Stream shape | Format |
| --- | --- | --- |
| [every](../cards/every.md) | `T* → Bool?` | Compact |
| [isEmpty](../cards/isEmpty.md) | `T* → Bool?` | Compact |
| [defaultIfEmpty](../cards/defaultIfEmpty.md) | `T* → (T \| D)*` | Compact |
| [throwIfEmpty](../cards/throwIfEmpty.md) | `T* → T*` | Compact |
| [ignoreElements](../cards/ignoreElements.md) | `T* → Never*` | Compact |
| [materialize](../cards/materialize.md) | `T* → Notification<T>*` | Compact |
| [dematerialize](../cards/dematerialize.md) | `Notification<T>* → T*` | Compact |
| [catchError](../cards/catchError.md) | `T* → (T \| R)*` | Compact |
| [onErrorResumeNextWith](../cards/onErrorResumeNextWith.md) | `T* → (T \| R)*` | Compact |
| [retry](../cards/retry.md) | `T* → T*` | Compact |
| [repeat](../cards/repeat.md) | `T* → T*` | Compact |
| [tap](../cards/tap.md) | `T* → T*` | Compact |
| [finalize](../cards/finalize.md) | `T* → T*` | Compact |
| [share](../cards/share.md) | `T* → T*` | Compact |
| [connect](../cards/connect.md) | `T* → R*` | Compact |

## Compatibility: aliases and older operator forms

**Session 7:** 15 cards · Compact notation complete

These compatibility forms are deprecated in the pinned version. Each card identifies its replacement and distinguishes pipeable imports from similarly named creation functions.

| Operator | Stream shape | Format |
| --- | --- | --- |
| [mapTo](../cards/mapTo.md) | `T* → R*` | Compact |
| [pluck](../cards/pluck.md) | `T* → (R \| undefined)*` | Compact |
| [flatMap](../cards/flatMap.md) | `T* → R*` | Compact |
| [concatMapTo](../cards/concatMapTo.md) | `T* → R*` | Compact |
| [mergeMapTo](../cards/mergeMapTo.md) | `T* → R*` | Compact |
| [switchMapTo](../cards/switchMapTo.md) | `T* → R*` | Compact |
| [combineAll](../cards/combineAll.md) | `(T*)* → (T[])*` | Compact |
| [exhaust](../cards/exhaust.md) | `(T*)* → T*` | Compact |
| [combineLatest](../cards/combineLatest.md) | `A* → (A × B)*` | Compact |
| [concat](../cards/concat.md) | `A* → (A \| B)*` | Compact |
| [merge](../cards/merge.md) | `A* → (A \| B)*` | Compact |
| [race](../cards/race.md) | `A* → (A \| B)*` | Compact |
| [zip](../cards/zip.md) | `A* → (A × B)*` | Compact |
| [partition](../cards/partition.md) | `T* → (T* × T*)` | Compact |
| [timeoutWith](../cards/timeoutWith.md) | `T* → (T \| R)*` | Compact |

## Compatibility: multicast and resubscription

**Session 8:** 8 cards · Compact notation complete

These compatibility forms are deprecated in the pinned version. Each card identifies its replacement and distinguishes pipeable imports from similarly named creation functions.

| Operator | Stream shape | Format |
| --- | --- | --- |
| [multicast](../cards/multicast.md) | `T* → Connectable<T>` | Compact |
| [publish](../cards/publish.md) | `T* → Connectable<T>` | Compact |
| [publishBehavior](../cards/publishBehavior.md) | `T* → Connectable<T>` | Compact |
| [publishLast](../cards/publishLast.md) | `T* → Connectable<T>` | Compact |
| [publishReplay](../cards/publishReplay.md) | `T* → Connectable<T>` | Compact |
| [refCount](../cards/refCount.md) | `Connectable<T> → T*` | Compact |
| [repeatWhen](../cards/repeatWhen.md) | `T* → T*` | Compact |
| [retryWhen](../cards/retryWhen.md) | `T* → T*` | Compact |

## Additional public alias

The deprecated pipeable export below shares its implementation file with `onErrorResumeNextWith`. Its own seven-field block brings the index to **114 public names without adding a duplicate source-file card**. The linked section distinguishes it from the root creation function.

| Public name | Owning card | Entry point |
| --- | --- | --- |
| [onErrorResumeNext](../cards/onErrorResumeNextWith.md#alias-onerrorresumenext) | [onErrorResumeNextWith](../cards/onErrorResumeNextWith.md) | `rxjs/operators` |

## Suggested reading paths

- **Local versus whole-stream cardinality:** map → filter → take → reduce.
- **Remembered values:** pairwise → scan → reduce → distinctUntilChanged.
- **Arrays and live streams:** buffer → window → bufferCount → windowCount → groupBy.
- **Flattening policies:** mergeAll / mergeMap → concatAll / concatMap → switchAll / switchMap → exhaustAll / exhaustMap.
- **Timing triggers:** debounce → throttle → audit → sample, then their timed forms.
- **Joining values:** mergeWith → concatWith → zipWith → combineLatestWith → withLatestFrom.
- **Execution ownership:** takeUntil → switchMap → share → shareReplay → connect.
- **Termination and recovery:** materialize → dematerialize → catchError → retry → repeat.

## Detailed expansion

The [session plan](session-plan.md) and [session manifest](../planning/operator-sessions.json) track expansion of existing compact cards into detailed cards. `compactOperators` records notation coverage; `completedOperators` records detailed completion. Sessions 1–2 are detailed, and Sessions 3–8 remain available for that expansion.

See the [notation guide](notation.md#8-compact-operator-notation), [validation record](validation.md), and [pinned source inventory](../planning/source-inventory.json) for the conventions and evidence behind this collection.
