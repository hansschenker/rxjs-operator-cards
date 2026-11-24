# distinct

```text
Operator:       distinct
Stream shape:   T* → T*
State:          Set<K> — keys already emitted
Initial state:  Empty set
Value step:     (Set<K> × T) → (Set<K> × Option<T>) — emit only a previously unseen key
Ownership:      One key set and optional flush subscription per operator subscription
Lifecycle:      Source completion/error terminates; flush next clears keys; flush error forwards; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** distinct(keySelector?, flushes?), Observable flush notifier when supplied · **Format:** Detailed

> Emit the first value for each key until a flush event clears the remembered keys.

## Type shape

```text
distinct :: (T → K) → U* → (T* → T*)
```

This explanatory form shows the selector and optional flush stream explicitly. Omitting the selector uses each payload itself as its key; omitting flushes keeps remembered keys for the entire connection. A flush payload's contents are irrelevant. Accepted output is the original `T`, not its selected `K`.

## Intuition

Incoming records may repeat the same identifier. Publish the first record for each identifier during a collection period. A separate reset signal starts a new period by forgetting the identifiers already seen. Selecting the identifier is application logic; remembering membership and responding to resets are the operator's rules.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Select its key; emit the original value only if that key is absent from the remembered set. |
| Whole connection | `T* → T*`; there may be many accepted values, and a previously emitted key can reappear after a flush. |

A source event produces at most one output, assuming its selector returns normally. Neither a flush nor normal completion emits a buffered collection. There is no eventual-output guarantee if the source remains silent.

## Remembers

`Set<K>` stores accepted keys since the last flush, initially none. `K = T` without a selector. The key is added before the value is forwarded. Membership uses native JavaScript `Set` equality, SameValueZero: repeated `NaN` is the same key, `+0` and `-0` are the same key, and object keys compare by identity.

`Option<T>` describes the local result: `Some(value)` forwards it; `None` forwards nothing. Each downstream connection owns its own set and optional flush subscription. Without resets, the set can grow with the number of distinct keys.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Create an empty set; subscribe to the source before subscribing to optional flushes. |
| Source next, unseen key | Add the key, then emit the original value. |
| Source next, known key | Keep the set unchanged; emit nothing. |
| Flush next | Clear the set without emitting a value. |
| Flush complete | End that notifier connection; do not clear keys or complete the output. |
| Source complete | Complete downstream and close the flush connection. |
| Source, selector, or flush error | Error downstream and close owned connections. |
| Downstream close | Close source and flush connections silently. |

## Time, cancellation, and sharing

- **Time:** Source and flush notifications determine when keys are checked and forgotten. No timer or scheduler is added. Synchronous source work can happen before the flush subscription exists.
- **Cancellation:** Completion, error, or downstream closure ends owned connections. Closing sends no extra completion and does not undo emitted records or stop unrelated consumers of a shared producer.
- **Sharing:** No sharing is introduced. Separate subscribers do not share their remembered keys unless an explicit sharing boundary shares this operator's output.

## Worked trace

Illustrative times are virtual milliseconds from connection. Both inputs are cold. `A1`, `A2`, `A3`, and `A4` are different records whose selected `id` is A; B1 has id B. Flush values carry no business data.

| Time | Event | Output and remembered keys |
| --- | --- | --- |
| 10 | Source A1 | Emit A1; `{A}`. |
| 20 | Source B1 | Emit B1; `{A, B}`. |
| 30 | Source A2 | Suppress; `{A, B}`. |
| 40 | Flush next | No output; `{}`. |
| 50 | Source A3 | Emit A3; `{A}`. |
| 60 | Flush complete | No output; `{A}` remains. |
| 70 | Source A4 | Suppress; `{A}`. |
| 80 | Source complete | Complete downstream. |

## Verification obligations

- Check the trace, original payload delivery, reset, and inert flush completion.
- Check repeated `NaN`, signed zeros, and object identity.
- Check selector failure and flush error terminate the result.
- Check independent sets, notifier teardown, and source-before-flush subscription order.

## Common confusion

`distinct` remembers all keys since the latest flush, not only the previous accepted key. Without flushing, `A, B, A` yields `A, B`; default `distinctUntilChanged` yields all three. Their equality also differs for `NaN`: native Set suppresses repeated NaN, while the default `===` comparison in `distinctUntilChanged` does not.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/distinct)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinct.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
