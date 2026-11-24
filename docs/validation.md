# Validation record

**Latest review:** 24 November 2026 · **Reference:** RxJS 7.8.2 · **Runtime:** Node.js v24.22.3

## Original 14-card checkpoint

The original first-edition checkpoint contained 14 worked cards. Its review combined inspection of the pinned RxJS implementation with 24 executable scenarios using `VirtualTimeScheduler` and assertions on observed values, terminal notifications, and selected connection lifetimes.

All 24 scenarios passed. These were focused checks performed while preparing the collection, not a claim of an exhaustive or repository-installed automated test suite.

## Executed scenarios

| Area | Scenarios | What was checked |
| --- | --- | --- |
| Value transformation and selection | 2 | `map` doubles each input in order; `filter` forwards only the odd inputs. |
| Accumulation | 4 | Seeded `scan` publishes running sums; seeded `reduce` publishes the final sum; empty input emits no `scan` seed but does emit the `reduce` seed. |
| Count and notifier boundaries | 4 | `take(2)` closes after its second output; `take(0)` never connects upstream; `takeUntil` closes on a notifier value; a synchronous notifier value can prevent source subscription. |
| Grouping | 3 | `buffer` publishes arrays and flushes at completion; `window` publishes its initial live stream and routes values between boundaries; outer window closure does not complete an independently held inner subscription, and a later subscription to the disposed window throws. |
| Inner-stream coordination | 4 | The common two-input trace distinguishes overlapping `mergeMap`, queued `concatMap`, replacing `switchMap`, and busy-gated `exhaustMap`; projection admission times are asserted. |
| Timing | 4 | `debounceTime` releases after a quiet interval, flushes on normal completion, discards on error, and cancels pending delivery on consumer closure. |
| Sharing and replay | 3 | Concurrent observers share one connection and retain a completed cache; the last observer leaving an unfinished source resets it; source error allows a fresh execution. |
| **Total** | **24** | **All passed against RxJS 7.8.2.** |

The sources used scheduled events relative to subscription. Their teardown canceled outstanding scheduled actions. Event order was explicit, including an inner's value before its completion at the same virtual time. The checks did not use real-time waiting.

## Source review

Each card identifies its scope and links the official documentation and version-pinned implementation. Shared helpers were inspected where behavior depends on accumulation, inner admission, error handling, or replay reset. Particular attention went to:

- The different emission policies of seeded `scan` and `reduce`.
- Notifier `next` versus notifier completion, and source/notifier subscription order.
- Live window ownership and the difference between completion and disposal.
- Projection admission and cancellation ordering in the four flattening policies.
- Completion flushing and scheduled-action reuse in `debounceTime`.
- Replay retention, reference counting, and error reset in `shareReplay`.

The [reference list](references.md) links these implementation helpers. The notation and design received a separate consistency review, including preservation of nested streams and the distinction between local and whole-stream cardinality.

## Documentation checks

The collection was checked for the ten required card sections, balanced fenced code blocks, resolvable relative file links, and agreement between the 14 card files and the operator index. Template placeholders belong only in the reusable template.

## Limits of this validation

The checks cover the selected scopes and illustrative scenarios. They do not establish formal equivalence, complete overload coverage, arbitrary scheduler/reentrancy behavior, or compatibility with another RxJS version. Verification obligations in individual cards also describe useful future cases; their presence does not mean every obligation has already been executed.

See the [operator index](operator-index.md), [writing contract](card-writing-contract.md), and [coverage roadmap](roadmap.md).

## Session 1 completion and planning update

On 23 November 2026, `startWith` was added as card 15. Six additional focused checks passed against RxJS 7.8.2:

1. Prefixes arrive synchronously in argument order before source subscription; normal source delivery and completion follow.
2. A source error follows the prefixes, preserves its error identity, and runs teardown.
3. Closing during the first prefix suppresses later prefixes, prevents source subscription, and sends no completion.
4. An empty source completes after prefixes; an empty prefix list mirrors the source.
5. Closing after source connection runs teardown without completion.
6. Array and function arguments remain single payloads, retain references across connections, and are not expanded or invoked.

This brings the recorded behavioral checks to **30 passing scenarios across 15 cards**. The new card's timed trace is illustrative; the six executed checks cover its synchronous ordering and lifecycle behavior.

The [session plan](session-plan.md) was also checked against the upstream inventory at `e5351d02e225e275ac0e497c7b66eaa5f0c88791`:

- 117 TypeScript files: 113 operator source files and four internal helpers.
- All 117 Git blob hashes match the installed RxJS 7.8.2 source files.
- All 113 operator files are assigned once, with no duplicates, omissions, or helpers in the batches.
- Session sizes are 15, 15, 15, 15, 15, 15, 15, and 8.
- The official export indexes identify 114 public operator names; the additional `onErrorResumeNext` alias has an explicit home in the `onErrorResumeNextWith` card.
- Fifteen current cards are marked complete; 98 are planned. Future cards and their checks are not being represented as finished.

The current files, relative links, index entries, and manifest progress were checked after this update. See [source-inventory.json](../planning/source-inventory.json) and [operator-sessions.json](../planning/operator-sessions.json) for the recorded inputs and assignment.

## Compact operator notation update — 24 November 2026

All 15 completed cards now begin with the same seven fields: Operator, Stream shape, State, Initial state, Value step, Ownership, and Lifecycle. The reusable template, notation guide, design, and writing instructions use the same format.

The compact summaries were reviewed against the cards' scoped RxJS 7.8.2 behavior and the relevant implementations. This included indexed value processing, seeded accumulation, prefix subscription order, notifier boundaries, live windows, scheduler-owned pending values, inner admission, and shared replay state.

Documentation checks passed:

- Exactly one seven-field block occurs immediately after each of the 15 operator titles, with consistent label order and alignment.
- The template contains the same seven fields, and no template placeholders remain in completed cards.
- All pre-existing card content is preserved, including all ten required detailed sections.
- Markdown fences are balanced, 152 relative links resolve, and the new compact-notation anchors exist.
- `git diff --check` reports no whitespace errors.

The collection still has 15 completed cards and 98 planned cards. The 30 behavioral scenarios recorded above remain the existing runtime-validation baseline; this documentation update was checked through structure validation and source review.

## Complete compact-notation coverage — 24 November 2026

The remaining 98 operator source files now have compact cards. Each begins with the requested seven fields and includes an explicit scope, local state definitions, essential behavior notes, and version-pinned implementation references. The extra deprecated pipeable `onErrorResumeNext` name has a separate seven-field block inside `onErrorResumeNextWith`.

| Check | Result |
| --- | --- |
| Operator card files match the pinned inventory | 113, with no missing or extra files |
| Public-name compact blocks | 114 unique names, including the additional alias |
| Existing detailed cards | All 15 unchanged from the preceding merged checkpoint |
| New compact cards | 98 with required metadata, notes, and references |
| Session assignments and coverage | Every source-file operator assigned once; compact and detailed progress recorded separately |
| Complete index | Every card and the extra alias linked; stream shapes agree with their headers |
| Local Markdown links and anchors | 581 checked, all resolve |
| Reference-source verification | All 117 recorded Git blob hashes match the local RxJS 7.8.2 source files |
| Markdown structure and whitespace | Balanced fences, aligned seven-field blocks, no card placeholders, and clean whitespace checks |

All 98 new operators were reviewed against their pinned implementations and relevant helpers, then independently reviewed across the operator families. The review covered notifier next versus completion, source/inner subscription order, pending-value disposal and flushing, live window/group ownership, join readiness and termination, resubscription triggers, and shared or connectable state retention. Corrections made during review included clearing sample state before delivery, restricting simple retry/repeat count models to integers, and qualifying teardown ordering for legacy notifier-driven resubscription.

The seven fields describe each card's stated scope. Specialized overloads and configurations are identified explicitly rather than inferred from a broad stream shape. Compact completion is now recorded for all 113 source-file cards; only the original 15 remain marked as detailed cards, with their worked traces and verification obligations. The index, plan, roadmap, writing instructions, and templates use that distinction consistently.

No new runtime scenarios were executed for this documentation pass. The 30 passing scenarios recorded above remain the existing runtime-validation baseline; this update adds complete notation coverage, source review, and documentation validation.

## Session 2 detailed expansion — 24 November 2026

All 15 Session 2 compact cards were expanded into detailed cards. Their seven-field headers remain unchanged; each detailed body supplies the exact ten sections, an explicit scope, a worked trace, and verification obligations. The collection now has **30 detailed cards and 83 compact-only cards**, with compact notation still complete for all 113 source files and 114 public names.

### Executed runtime scenarios

**117 named scenarios passed; zero failed**, against actual **RxJS 7.8.2** exports using **Node.js v24.22.3**. There are **111 operator-focused scenarios and six cross-card comparison scenarios**. Each of the 15 primary worked traces was executed. The checks use synchronous sources, `TestScheduler`, or `VirtualTimeScheduler`; virtual frames are interpreted as illustrative milliseconds. Source connection, predicate calls, notifications, and teardown are observed where relevant.

| Operator | Scenarios | Main distinctions checked |
| --- | ---: | --- |
| `skip` | 6 | Prefix count, zero count, short/empty completion, errors, cancellation, independent indexes |
| `takeLast` | 6 | Completion-time suffix, zero-count subscription bypass, error/closure discard, cancellation during flushing |
| `skipLast` | 6 | Arrival-count delay, zero count, short/empty sources, no tail flush after error/closure |
| `takeWhile` | 7 | First rejection, empty/all-accepted input, source/predicate errors, cancellation, scoped inclusive comparison |
| `skipWhile` | 6 | Boundary value passes, predicate stops after opening, both phases, errors and cancellation |
| `skipUntil` | 9 | Notifier-first setup, next versus completion, both error origins, teardown, independent gates, synchronous-error setup |
| `first` | 9 | Early selection, no-match errors, predicate arguments, real/default undefined, silent cancellation |
| `last` | 8 | Retained candidate, completion-time output, empty/no-match errors, default variants, failure and cancellation |
| `single` | 9 | Sole result, three failure classes, duplicate matches, candidate discard, optional predicate |
| `elementAt` | 9 | Zero-based selection, early closure, short-source error, construction-time negative-index error, defaults |
| `find` | 8 | Value search, undefined fallback, predicate arguments, errors/closure, explicit reentrant boundary |
| `findIndex` | 8 | Index search, -1 fallback, predicate arguments, errors/closure, explicit reentrant boundary |
| `distinct` | 7 | Set equality, payload identity, flush reset/completion, setup order, per-connection state |
| `distinctUntilChanged` | 7 | Last accepted key, selector/comparator calls, equality, failures, state before delivery |
| `distinctUntilKeyChanged` | 6 | Retained object reference, property comparison, mutation, getters, failures, numeric equality |
| Cross-card comparisons | 6 | Count-policy timing, predicate gates, matching results, no-match/empty outcomes, distinct history |
| **Total** | **117** | **All passed** |

The [machine-readable validation record](../planning/session-02-validation.json) lists every check name, its operator, pass status, and scope limitations. The [Session 2 comparison guide](sessions/session-02.md) presents the shared examples as readable tables.

The previous checkpoint recorded 30 passing scenarios. This session adds 117 distinct named scenarios, bringing the historical record to 147; repeated runs of the same scenario are not counted again.

### Findings and scope refinements

- `takeLast(0)` completes without connecting upstream; `skip(0)` and `skipLast(0)` still observe their sources.
- `takeLast` waits for normal completion and emits values individually. `skipLast` releases earlier values when newer values arrive and never flushes its held tail.
- `takeWhile` ends on its first rejection. `skipWhile` forwards that boundary value and stops calling its predicate.
- `skipUntil` opens on notifier `next`, not completion. In 7.8.2, a synchronous notifier error can still be followed by a custom source initializer being called with a closed subscriber; no source values reach downstream.
- `single` distinguishes `EmptyError`, `NotFoundError`, and `SequenceError`. A matching value remains private until normal completion confirms uniqueness.
- `find` and `findIndex` emit actual absence values on normal no-match completion, while their errors and cancellation do not synthesize those results.
- The search cards now state the non-reentrant assumption of their at-most-one model. Two checks demonstrate the 7.8.2 boundary: a result handler that synchronously emits another matching source value can receive another result before completion.
- `distinctUntilKeyChanged` retains an object reference and rereads its property; it does not retain a primitive property snapshot. This distinction was checked with mutation as well as ordinary immutable records.

### Review and limits

Every card was source-reviewed, with an independent semantic review of the three groups and the comparison guide. No compact header required changing. Source verification again matched all **117 recorded source-file Git blob hashes** against the installed RxJS 7.8.2 files.

The scenarios exercise the cards' declared scopes and several explicitly discussed comparison variants, such as defaults and inclusive `takeWhile`. They are not exhaustive overload, scheduler, reentrancy, or liveness proofs. State columns in traces are explanatory models; the runtime checks inspect observable behavior rather than private state. Verification obligations remain useful additional review cases and are not all implied to have been executed.

The session and coverage manifests, index formats and stream shapes, required card sections, preserved compact headers, local links/anchors, and file boundaries were checked. The results are recorded with the runtime evidence. These are executed review checks, not a claim that a repository-installed test runner or automatic generator was added.
