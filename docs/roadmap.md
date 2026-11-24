# Coverage roadmap

## Current checkpoint — 24 November 2026

**Compact notation is complete for all 113 operator source files and 114 public names.** Every card starts with the same seven fields: Operator, Stream shape, State, Initial state, Value step, Ownership, and Lifecycle.

| Format | Cards | Coverage |
| --- | ---: | --- |
| Detailed | 30 | Compact overview plus explanation, event/action table, worked trace, and verification obligations |
| Compact | 83 | Compact overview, explicit scope, local notation and behavior notes, and primary references |
| **Total** | **113** | **The complete pinned operators-directory inventory** |

The extra deprecated pipeable alias `onErrorResumeNext` has a separate seven-field block in the `onErrorResumeNextWith` card. The [operator index](operator-index.md) links all source-file cards and that alias.

Behavior remains pinned to **RxJS 7.8.2**. The [source inventory](../planning/source-inventory.json) records 113 operator files and four internal helpers. The helpers do not receive public operator cards; creation functions and conversion utilities remain outside this inventory.

**Sessions 1 and 2 are detailed and complete.** The [Session 2 comparison guide](sessions/session-02.md) records the learning comparisons. Session 3 is the next batch, with its original target date of 25 November 2026.

## Detailed expansion

The [eight-session plan](session-plan.md) now organizes expansion of existing compact cards. Its original dates remain planning targets, not scheduled automation.

| Session | Focus | Operators | Compact notation | Detailed expansion |
| --- | --- | ---: | --- | --- |
| [1](session-plan.md#session-1) | Core behavior and state | 15 | Complete | Complete |
| [2](session-plan.md#session-2) | Selection, boundaries, and distinct values | 15 | Complete | Complete |
| [3](session-plan.md#session-3) | Time, scheduling, and delivery boundaries | 15 | Complete | Planned |
| [4](session-plan.md#session-4) | Grouping, aggregation, and sequence comparison | 15 | Complete | Planned |
| [5](session-plan.md#session-5) | Combining, flattening, and expanding streams | 15 | Complete | Planned |
| [6](session-plan.md#session-6) | Completion, recovery, effects, and shared execution | 15 | Complete | Planned |
| [7](session-plan.md#session-7) | Compatibility: aliases and older operator forms | 15 | Complete | Planned |
| [8](session-plan.md#session-8) | Compatibility: multicast and resubscription | 8 | Complete | Planned |

The first six sessions contain the 90 current operator files; the final two contain the 23 deprecated forms. Compatibility cards identify the pipeable import and distinguish it from similarly named creation functions.

## Progress rules

- `compactOperators` in the [session manifest](../planning/operator-sessions.json) records completed notation coverage.
- `completedOperators` and session `status` record detailed-card completion, including the reviewed explanation and trace.
- Preserve each compact overview when expanding its card, refining it only with supporting source evidence.
- Record actual checks separately from suggested verification obligations. Source review does not claim that all runtime scenarios were executed.
- Retain the project's contributor attribution when committing additions or expansions.

## Later extensions

- Add explicitly scoped variants for additional overloads, scheduler choices, and sharing policies.
- Derive Mealy transition tables and test scenarios from reviewed event/action specifications.
- Consider machine-readable behavioral specifications after the prose rules stabilize; the current manifests describe coverage rather than executable semantics.
- Add a browseable site if requested; the Markdown collection remains directly usable.
