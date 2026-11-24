# Contributing operator cards

1. Choose the next compact card awaiting detailed expansion from the [session plan](docs/session-plan.md) and [progress manifest](planning/operator-sessions.json), or identify an explicitly scoped variant separately. All 113 inventoried operators already have compact notation.
2. Read the [notation guide](docs/notation.md) and [writing contract](docs/card-writing-contract.md).
3. Expand the existing file using [the detailed template](templates/operator-card.md), preserving its reviewed seven-line header and useful notes. For an explicitly requested compact-only addition or new scoped variant, use [the compact template](templates/compact-operator-card.md).
4. Inspect the RxJS 7.8.2 implementation and cite the exact source file. Official documentation is a useful companion; use implementation behavior to resolve simplified prose.
5. Describe values and triggers before implementation. Make time, cancellation, and sharing explicit.
6. For detailed expansions, include an illustrative trace and a few observable verification obligations. Check any newly asserted corner case against the reference implementation in either format.
7. Keep [the operator index](docs/operator-index.md) and progress manifest current. `compactOperators` records compact notation; `completedOperators` and session `status` record detailed expansion. Add to the latter only after the detailed-card completion criteria are met.

## Review questions

- Does the opening block contain all seven fields in the standard order, agree with the accompanying explanation, and define any operator-specific state aliases?
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
