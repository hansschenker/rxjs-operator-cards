# Contributing operator cards

1. Choose the next pending operator from the [session plan](docs/session-plan.md) and [progress manifest](planning/operator-sessions.json), or identify an explicitly scoped variant separately.
2. Read the [notation guide](docs/notation.md) and [writing contract](docs/card-writing-contract.md).
3. Copy [the template](templates/operator-card.md) to `cards/<operatorName>.md`. Fill in its opening seven-line compact notation block, preserving the field order and tailoring the state, ownership, and lifecycle to the card's scope.
4. Inspect the RxJS 7.8.2 implementation and cite the exact source file. Official documentation is a useful companion; use implementation behavior to resolve simplified prose.
5. Describe values and triggers before implementation. Make time, cancellation, and sharing explicit.
6. Include an illustrative trace and a few observable verification obligations. Check any newly asserted corner case against the reference implementation.
7. Add the accepted card to [the operator index](docs/operator-index.md), update its session's completed names/status, and keep collection counts and validation evidence current.

## Review questions

- Does the opening block contain all seven fields in the standard order, agree with the detailed card, and define any operator-specific state aliases?
- Is the local `1 → 0..1` rule kept distinct from the whole-stream cardinality?
- Are errors, normal completion, and consumer closure separate?
- Does a notifier trigger on `next`, `complete`, or another event? Is that verified?
- Does an inner stream start immediately, queue, replace another, or get ignored?
- Is a supplied projection called only when work is admitted?
- Are source subscription order and synchronous notifiers covered where they matter?
- Does the trace specify clocks, connection-relative time, and live versus cold sources?
- Is sharing introduced explicitly? Are replay and reset rules stated?
- Are all links valid, and are obligations distinguished from checks actually performed?

Use small, pure functions in any TypeScript examples; do not use JavaScript classes or enums. Keep effects at clearly identified connection/output boundaries. A documentation-only addition does not require introducing a runtime, build system, or website.

Preserve source attribution and the named contributors. Use the co-author trailer in [CONTRIBUTORS.md](CONTRIBUTORS.md) for contributions containing the credited AI-assisted work.
