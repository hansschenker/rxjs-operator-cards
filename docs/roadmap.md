# Coverage roadmap

## First edition: captured

- Reusable operator-card template and writing contract.
- Cω-inspired notation with explicit Pull, Push, and scheduler models.
- Fourteen worked cards spanning values, state, boundaries, grouping, timing, flattening, and replay.
- Source references, review obligations, and a record of concrete verification.
- Project and AI contribution credit in the README, maintained contributor list, and commit trailers.

## Next families

| Family | Candidate cards |
| --- | --- |
| Bounded selection | `first`, `last`, `single`, `elementAt`, `find`, `findIndex`, `takeWhile`, `takeLast`, `skip`, `skipWhile`, `skipUntil`, `skipLast` |
| Timing | `debounce`, `throttle`, `throttleTime`, `audit`, `auditTime`, `sample`, `sampleTime`, `delay`, `delayWhen`, `timeout`, `observeOn`, `subscribeOn` |
| Grouping | `bufferCount`, `bufferTime`, `bufferToggle`, `bufferWhen`, `windowCount`, `windowTime`, `windowToggle`, `windowWhen`, `groupBy` |
| Combination | `mergeWith`, `concatWith`, `combineLatestWith`, `zipWith`, `withLatestFrom`, `raceWith`, `pairwise` |
| Higher-order behavior | `mergeAll`, `concatAll`, `switchAll`, `exhaustAll`, `mergeScan`, `switchScan`, `expand` |
| Error and lifecycle | `catchError`, `retry`, `repeat`, `finalize`, `materialize`, `dematerialize`, `throwIfEmpty`, `defaultIfEmpty` |
| State and aggregation | `toArray`, `count`, `min`, `max`, `every`, `isEmpty`, `distinct`, `distinctUntilChanged`, `distinctUntilKeyChanged` |
| Sharing | `share`, `connect`; explicit variants and reset policies for `shareReplay` |

These are candidates, not completed documentation or an exhaustive API enumeration. Build a versioned inventory before claiming full coverage. Keep deprecated APIs in a separately labeled compatibility section if they are added.

## Later improvements

1. Extend coverage using the existing template and pinned-source review.
2. Add a machine-readable representation once the prose card structure is stable.
3. Derive Mealy transition tables and test scenarios from reviewed event/action specifications.
4. Add a browseable site if requested; the Markdown repository remains directly usable.

Creation functions and conversion utilities may use an adapted card with an explicit connection protocol. They should not be forced into the pipeable `T* → R*` shape.
