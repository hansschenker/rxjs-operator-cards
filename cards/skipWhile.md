# skipWhile

```text
Operator:       skipWhile
Stream shape:   T* → T*
State:          Bool × Nat — forwarding gate and predicate index
Initial state:  (false, 0)
Value step:     (S × T) → (S × Option<T>) — open gate on first rejection; then always forward
Ownership:      One state per operator subscription
Lifecycle:      Forward source completion/error; predicate errors terminate; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skipWhile(predicate) · **Format:** Detailed

> Skip the initial accepted run, then forward the first rejected value and every later value.

## Type shape

```text
skipWhile :: (T! → Bool!) → T* → T*
```

The predicate decides whether initial skipping continues. This explanatory signature omits its zero-based index. Its result decides admission; source payloads pass through unchanged.

## Intuition

Readings begin below a readiness threshold. Ignore that initial stretch; when a reading first reaches readiness, start displaying it and every later reading. A later drop below the threshold does not restart the waiting phase. The predicate defines readiness; the operator remembers that forwarding has begun.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| While skipping, predicate returns true | Discard the value and remain in the skipping phase. |
| While skipping, predicate first returns false | Open the gate and forward this boundary value. |
| After the gate opens | Forward every source value without calling the predicate. |
| Whole connection | Remove an initial predicate-accepted prefix, then preserve all remaining observed values in order. |

An empty or entirely accepted finite source completes without outputs. A source can remain in the skipping phase forever. The type shape gives no guarantee that the gate opens, that a value arrives, or that the connection completes.

## Remembers

`S = Bool × Nat` contains a forwarding gate and predicate index. Initially `(false, 0)`, it advances the index on each predicate call. The first false result changes the gate to true. Once open, neither the predicate nor its index advances again.

`Option<T>` describes the local output decision: `None` means no emitted value; `Some(value)` means unchanged forwarding. It does not turn skipped values into `undefined`. State is independent per connection, but mutable data captured by the predicate belongs to that external closure.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Initialize `(false, 0)` and subscribe upstream. |
| Source next while skipping | Call the predicate with value and index; advance the index. True discards; false opens the gate and forwards. |
| Source next while forwarding | Leave gate and index unchanged; forward without predicate evaluation. |
| Predicate throws while skipping | Error downstream and close upstream; emit no value for that step. |
| Source complete | Complete downstream from either phase and release upstream. |
| Source error | Forward the error from either phase and release upstream. |
| Downstream close | Close upstream silently; perform no further predicate evaluations. |

There is no boundary notifier, timer, or inner connection.

## Time, cancellation, and sharing

- **Time:** Predicate checks and admitted delivery follow source arrivals. The boundary value is forwarded immediately during its source notification. No scheduler or deliberate delay is introduced.
- **Cancellation:** Closing in either phase stops this connection and runs upstream teardown. It does not undo effects or necessarily stop a producer with other owners.
- **Sharing:** No sharing is introduced. A late connection has a fresh closed gate even if an earlier observer of the same live source has already started forwarding.

## Worked trace

Illustrative cold source; times are milliseconds from connection, with setup before the first value at zero. Use `value < 3`. The later value `1` demonstrates that the predicate is no longer consulted after the boundary.

| Time | Source event | State after event | Output |
| --- | --- | --- | --- |
| 0 | `next(1)` | `(false, 1)` | None |
| 10 | `next(2)` | `(false, 2)` | None |
| 20 | `next(3)` | `(true, 3)` | `next(3)` |
| 30 | `next(1)` | `(true, 3)` | `next(1)` |
| 40 | `next(4)` | `(true, 3)` | `next(4)` |
| 50 | `complete` | Connection ended | `complete` |

## Verification obligations

- Check the first false result is forwarded and permanently opens the gate.
- Check predicate calls and indexes stop after opening, including when later values would pass the predicate.
- Check first-value rejection, all-skipped completion, and empty input.
- Check predicate failure plus source errors and closure in both phases.
- Check independent gates on separate live-source connections.

These are obligations, not an assertion that every scenario has been tested.

## Common confusion

`skipWhile(p)` does not behave like continually filtering by `!p`: after the first rejection it keeps all later values, even ones satisfying `p`. In contrast, `takeWhile(p)` omits that rejection and ends the connection. Here, “rejection” means rejection of continued skipping.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/skipWhile)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skipWhile.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
