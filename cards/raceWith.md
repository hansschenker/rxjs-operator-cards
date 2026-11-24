# raceWith

```text
Operator:       raceWith
Stream shape:   A* → (A | B)*
State:          Option<InputId> × InputId[]
Initial state:  (None, [])
Value step:     (S × InputNext<A, B>) → (S × Action[]) — first next selects a winner and closes competitors
Ownership:      One winner selection and owned input connections per operator subscription
Lifecycle:      An early complete/error can end the race; after selection follow winner; cancellation closes all silently
```

**Family:** Combination · **Reference:** RxJS 7.8.2 · **Scope:** `raceWith(other)`; one Observable `B*` · **Format:** Compact

> The first input to deliver a value wins, and its future values alone flow onward.

## Notation and scope

- `InputId = Source | Other`; `S` holds the optional winner and connected input identities. `InputNext<A, B>` tags a next with its input identity.
- Before selection, the first next closes other connected inputs and forwards its payload. Later next values come only from the winner.
- A completion or error before any next is forwarded immediately and ends the result; an empty first input can complete the result before another input is even subscribed.
- Setup connects inputs in order while no synchronous winner or termination has occurred. The supplied `other: B*` is a fixed argument.
- With no additional input, `raceWith()` is identity. More inputs extend the same winner policy.
- No scheduler or sharing is added. Closing losers prevents later delivery through this result; it does not undo values already observed.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/raceWith.ts)
- [Race implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/race.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
