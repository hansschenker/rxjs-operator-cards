# Validation record

**Review date:** 23 November 2026 · **Reference:** RxJS 7.8.2 · **Runtime:** Node.js v24.22.3

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
