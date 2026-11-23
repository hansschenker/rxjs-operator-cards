# Coverage roadmap

## Current checkpoint

**Session 1 is complete: 15 cards.** The initial 14 cards are joined by `startWith`, completing today's batch on **23 November 2026**. There are **98 planned cards** remaining.

The [complete session plan](session-plan.md) assigns all **113 operator source files** in the requested RxJS 7.x directory. Behavior remains pinned to **RxJS 7.8.2**; all 117 files in the audited directory, including four helpers, match that release's installed source by Git blob hash.

## Eight-session sequence

The proposed cadence is one session per calendar day. Session 2 is tomorrow, **24 November 2026**. Later dates are targets and can move if a session needs additional review.

| Session | Target date | Focus | New cards | Status |
| --- | --- | --- | ---: | --- |
| [1](session-plan.md#session-1) | 23 November 2026 | Core behavior and state | 15 | Complete |
| [2](session-plan.md#session-2) | 24 November 2026 | Selection, boundaries, and distinct values | 15 | Planned |
| [3](session-plan.md#session-3) | 25 November 2026 | Time, scheduling, and delivery boundaries | 15 | Planned |
| [4](session-plan.md#session-4) | 26 November 2026 | Grouping, aggregation, and sequence comparison | 15 | Planned |
| [5](session-plan.md#session-5) | 27 November 2026 | Combining, flattening, and expanding streams | 15 | Planned |
| [6](session-plan.md#session-6) | 28 November 2026 | Completion, recovery, effects, and shared execution | 15 | Planned |
| [7](session-plan.md#session-7) | 29 November 2026 | Compatibility: aliases and older operator forms | 15 | Planned |
| [8](session-plan.md#session-8) | 30 November 2026 | Compatibility: multicast and resubscription | 8 | Planned |

Sessions 1–6 contain all 90 current operator files. Sessions 7–8 contain the 23 deprecated forms. The final session has eight new cards plus a collection review.

## Coverage rules

- Count one card per operator source file; every assigned name appears exactly once.
- Include explicit aliases and deprecated forms so older RxJS code remains understandable.
- Cover the extra deprecated pipeable `onErrorResumeNext` name inside the `onErrorResumeNextWith` card; this accounts for 114 public operator names in 113 source-file cards.
- Record `OperatorSubscriber`, `joinAllInternals`, `mergeInternals`, and `scanInternals` as internal helpers rather than public operators.
- State import paths for older pipeable APIs sharing names with creation functions.
- Treat deprecated overloads separately from whole-operator deprecation.
- Mark a card complete only within its declared and reviewed scope.

The [source inventory](../planning/source-inventory.json) records the upstream snapshot and file hashes. The [session manifest](../planning/operator-sessions.json) records ordered batches, completed names, dates, and status. These files support resuming the next session reliably.

## Each session delivers

1. Reviewed cards using the existing template and Cω-inspired notation.
2. Explicit time, cancellation, sharing, and termination behavior.
3. Worked traces and a record of checks actually performed.
4. Updated operator index and progress manifest.
5. A repository commit retaining the contributor attribution.

## After inventory coverage

- Add explicitly scoped variants for additional overloads and sharing policies.
- Derive Mealy transition tables and test scenarios from reviewed event/action specifications.
- Consider a machine-readable behavioral specification once the prose semantics are stable; the planning manifests describe coverage, not executable semantics.
- Add a browseable site if requested; the Markdown collection remains directly usable.

Creation functions and conversion utilities are outside this operators-directory inventory. They can use an adapted card with an explicit connection protocol in a later phase.
