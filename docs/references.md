# References and source attribution

## Discussion that inspired the repository

- [Erik Meijer's talk on duality](https://www.youtube.com/watch?v=h0EUrewSlkg). The associated conversation informed the teaching direction. This repository is an original synthesis, not a transcript of the talk.
- Hans Schenker and ChatGPT's operator-card discussion, 23 November 2026: combine a compact type shape with explicit event/action, time, cancellation, and sharing rules.

## Primary conceptual sources

1. Gavin Bierman, Erik Meijer, and Wolfram Schulte, [The Essence of Data Access in Cω: The Power is in the Dot!](https://www.microsoft.com/en-us/research/wp-content/uploads/2005/01/TheEssenceOfDataAccessInCwECOOP2005.pdf), ECOOP 2005. The stream discussion explains the original iterator-based interpretation and automatic flattening. The notation here deliberately preserves RxJS nested streams.
2. Erik Meijer, [Subject/Observer is Dual to Iterator](https://csl.stanford.edu/~christos/pldi2010.fit/meijer.duality.pdf), 2010. Background for the Pull/Push interface comparison and retained disposal capability.
3. Erik Meijer, Maarten Fokkinga, and Ross Paterson, [Functional Programming with Bananas, Lenses, Envelopes and Barbed Wire](https://maartenfokkinga.github.io/utwente/mmf91m.pdf), 1991. Background for separating reusable structural machinery from supplied functions.

## RxJS reference version

- [RxJS 7.8.2 source tree](https://github.com/ReactiveX/rxjs/tree/7.8.2).
- [Observable guide](https://rxjs.dev/guide/observable).
- [Observer interface](https://rxjs.dev/api/index/interface/Observer).
- [Scheduler guide](https://rxjs.dev/guide/scheduler).
- [GroupedObservable interface](https://rxjs.dev/api/index/interface/GroupedObservable).

Every completed card links its official operator documentation and pinned implementation. Source files are authoritative for the scoped version when broad documentation prose hides a corner case.

## Shared implementation helpers

- [OperatorSubscriber](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/OperatorSubscriber.ts): forwarding, error handling, and finalization of operator connections.
- [scanInternals](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/scanInternals.ts): shared accumulation machinery and distinct emission policies.
- [mergeInternals](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts): active inner count, buffering, admission, and completion.
- [share](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/share.ts) and [ReplaySubject](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/ReplaySubject.ts): ownership and replay behavior underlying `shareReplay`.

## Attribution boundary

The cards, examples, and writing template are original work prepared for this repository. References acknowledge the abstractions and implementation used to check them. They do not imply that the cited authors or the RxJS project contributed to or endorsed this repository.

Project contributions are recorded in [CONTRIBUTORS.md](../CONTRIBUTORS.md).
