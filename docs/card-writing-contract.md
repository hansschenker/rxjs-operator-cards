# Card writing contract

This repository captures the operator-card idea from Hans Schenker and ChatGPT's discussion on 23 November 2026. Its first edition documents RxJS 7.8.2.

## Teaching order

Use **Intuition → Specification → Verification**. Explain what flows, what changes when a value arrives, and why the operator is useful. Introduce code only after the behavior is clear. An operator does not know the business meaning of its payload; supplied functions carry that meaning.

## Required parts

Use the headings in [the template](../templates/operator-card.md): type shape; intuition; local versus whole-stream behavior; remembered state; event/action table; time, cancellation, and sharing; worked trace; verification obligations; common confusion; and primary references.

Keep each initial card concise enough to read independently. State the overload or policy being described. Optional overloads can be added later as explicit variants.

## Notation agreement

- `T` is one ordinary payload type. `T!` emphasizes one value in a local rule or function argument/result position.
- In stream positions, `T*` abbreviates `Observable<T>`; `T?` is an Observable with at most one `next`; `T!` and `T+` need explicit success/progress conditions when used for whole streams.
- Cardinality alone gives no completion or eventual-emission guarantee. Error or downstream closure can prevent values.
- `T[]` is one array payload; `(T[])*` is a stream of arrays; `(T*)*` is a stream of Observable descriptions. Never erase the nesting without a named flattening policy.
- `A | B` is a union, `A & B` an intersection, and `A × B` a pair. `combineLatest` does not produce an intersection merely by combining sources.
- Signatures are function-first mathematical descriptions, not replacement TypeScript declarations. Simplify to Observable inputs and name omitted overloads/index parameters where needed.

## Behavioral precision

- State, timers, inner notifications, completion, error, and closure all have explicit places in the card.
- Source completion, downstream completion, and downstream unsubscription are different events.
- A `next` on a notifier is not its completion. Verify the exact trigger.
- Ordinary pipeable operator state is normally per downstream connection. Existing live/shared sources keep their own ownership rules. `shareReplay` is a sharing boundary; windows are live inner streams.
- Closing an inner subscription does not necessarily abort an external Promise, shared producer, or already completed effect. Describe delivery closure and producer teardown separately.
- A scheduler supplies the clock and execution mechanism. Time operators apply rules using it; no operator creates a clock from nothing.
- Do not assert unrestricted operational equalities between `xMap(project)` and `map(project)` followed by `xAll`: projection timing, admission, errors, and effects can differ.
- A nonempty/one-result claim must state its normal-completion assumptions. Seeded `scan` does not emit the seed by itself; seeded `reduce` emits it on empty normal completion.

## Evidence

Reference official RxJS documentation and pinned 7.8.2 source for behavior. For traces, use distinct event times or explicitly state scheduler tie ordering. Label examples as illustrative; mark verification obligations as obligations. A later validation report may record concrete checks actually performed.

Historical Cω and the simplified Pull/Push/scheduler shapes are inspiration. This repository preserves nested RxJS streams and does not claim to implement Cω's original language semantics or a formal type checker.
