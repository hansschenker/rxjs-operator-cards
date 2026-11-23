# startWith

**Family:** Sequence prefix · **Reference:** RxJS 7.8.2 · **Scope:** Ordinary variadic prefix values; no deprecated scheduler overload

> Emit supplied starting values before connecting to the source, then forward its values so consumers have an initial representation while awaiting later updates.

## Type shape

```text
startWith :: P[] → T* → (P | T)*
```

`P[]` describes ordered prefix arguments, passed as `startWith(p1, p2, ...)`. Prefix and source types may differ. This explanatory signature is not a literal TypeScript declaration.

## Intuition

A counter display needs zero immediately, then incoming totals. Each connection receives the supplied zero before source subscription; the source supplies subsequent changes.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One prefix argument | Emit that payload once during connection setup, while downstream remains open. |
| One source event | Forward the source value unchanged. |
| Whole connection | With `k` prefix arguments and `n` source values, normal completion while still connected delivers `k + n` values. |

A nonempty prefix yields a nonempty output when downstream stays open to receive it. It does not guarantee completion: a source that remains open keeps the result open. With no prefix arguments, source delivery begins directly.

## Remembers

The operator retains the supplied prefix values. Each connection owns its progress through the prefix and subsequent source subscription. Objects are retained by reference, not cloned; separate connections can receive the same object.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Emit prefixes synchronously in argument order, then connect to the source if downstream remains open. |
| Source next | Forward the payload unchanged. |
| Source complete | Complete the output and release owned subscriptions. |
| Source error | Forward the error and release owned subscriptions; earlier prefixes remain delivered. |
| Downstream closes during prefixes | Stop remaining prefix delivery; never subscribe to the source. |
| Downstream closes after source connection | Release upstream without sending a terminal notification. |

No projection, notifier, or timer participates in this scope.

## Time, cancellation, and sharing

- **Time:** Prefixes arrive synchronously during subscription, before source subscription. Later values follow source timing; no scheduler is introduced.
- **Cancellation:** Closure can prevent source work from starting. Once connected, physical producer shutdown depends on its teardown and ownership; previous effects cannot be undone.
- **Sharing:** No sharing or replay is introduced. Each connection at this operator receives its prefixes. A live source can produce events before this operator reaches its source subscription.

## Worked trace

Illustrative cold source and prefixes `-1, 0`. Times are milliseconds from the result connection. The numbered setup steps establish ordering within time zero; source timing starts after those prefixes.

| Time | Event | Output |
| --- | --- | --- |
| 0, step 1 | Begin prefix delivery | `next(-1)` |
| 0, step 2 | Next prefix | `next(0)` |
| 0, step 3 | Connect to source | None |
| 10 | Source `next(1)` | `next(1)` |
| 20 | Source `next(2)` | `next(2)` |
| 30 | Source complete | `complete` |

## Verification obligations

- Check prefix order and that source subscription follows every delivered prefix.
- Check empty-source completion and source error after prefixes.
- Check closure during the first prefix prevents source subscription entirely.
- Check post-connection closure tears down upstream without completion.

These are behavioral obligations; recorded checks belong in the validation report.

## Common confusion

`startWith([a, b])` emits one array; `startWith(a, b)` emits two values. Argument expressions are evaluated when the operator is constructed, not afresh per connection. A function argument is emitted as a value, not invoked.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/startWith)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/startWith.ts)
- [RxJS 7.8.2 concatenation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/concat.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
