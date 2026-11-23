# Operator index

**Reference:** RxJS 7.8.2 · **Completed collection:** 15 cards · **Session 1:** Complete

The [session plan](session-plan.md) assigns all 113 operator source files in eight batches. The 15 cards below are complete within their declared scopes; 98 cards remain planned. Session 2 starts with selection and boundary behavior on 24 November 2026.

The signatures are explanatory and specialize some overloads. Read each card's scope and lifecycle rules. `T?` in an output position is still an Observable, with at most one emitted value.

| Operator | Type shape | Main distinction |
| --- | --- | --- |
| [map](../cards/map.md) | `(T! → R!) → T* → R*` | Transform each value |
| [filter](../cards/filter.md) | `(T! → Bool!) → T* → T*` | Select values |
| [scan](../cards/scan.md) | `((S! × T!) → S!) → S! → T* → S*` | Publish accumulated state after each input |
| [reduce](../cards/reduce.md) | `((S! × T!) → S!) → S! → T* → S?` | Publish accumulated state on normal completion |
| [take](../cards/take.md) | `Count → T* → T*` | Stop after a bounded count; `take(1)` refines output to `T?` |
| [takeUntil](../cards/takeUntil.md) | `U* → T* → T*` | A notifier value closes the source and completes output |
| [buffer](../cards/buffer.md) | `U* → T* → (T[])*` | Publish collected arrays |
| [window](../cards/window.md) | `U* → T* → (T*)*` | Publish live inner streams |
| [debounceTime](../cards/debounceTime.md) | `Duration → Scheduler → T* → T*` | Keep the latest pending value until a quiet interval |
| [mergeMap](../cards/mergeMap.md) | `(T! → R*) → T* → R*` | Allow overlap |
| [concatMap](../cards/concatMap.md) | `(T! → R*) → T* → R*` | Queue outer values |
| [switchMap](../cards/switchMap.md) | `(T! → R*) → T* → R*` | Replace the active inner connection |
| [exhaustMap](../cards/exhaustMap.md) | `(T! → R*) → T* → R*` | Ignore outer values while busy |
| [shareReplay](../cards/shareReplay.md) | `T* → T*` after choosing its replay policy | Share execution and replay cached values |
| [startWith](../cards/startWith.md) | `P[] → T* → (P \| T)*` | Emit supplied prefixes before connecting to the source |

## Suggested reading paths

- **Local versus global cardinality:** map → filter → take → reduce.
- **State and termination:** scan → reduce → debounceTime.
- **Nested values:** buffer → window → the four flattening policies.
- **Connection ownership:** takeUntil → switchMap → shareReplay.

## Next sessions

The [session plan](session-plan.md) contains the complete assignment and dates; [operator-sessions.json](../planning/operator-sessions.json) records progress. An operator absent from this table does not yet have a completed card. Planned operators are listed in the plan without links to nonexistent card files.
