# buffer

**Family:** Notifier grouping · **Reference:** RxJS 7.8.2 · **Scope:** Observable source and Observable closing notifier

> Collect values in an array, publish it on each boundary value, and begin collecting the next group.

## Type shape

```text
buffer :: N* → T* → (T[])*
```

The notifier `N*` supplies boundaries; payloads are ignored. Each output is an array of zero or more source values. Other `ObservableInput` forms are omitted.

## Intuition

Readings arrive continuously; pressing “collect” requests a batch. Publish the readings remembered since the previous request. Two requests with no intervening reading produce an empty second array: a boundary does not require a nonempty batch.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Append the value to the current array; emit nothing yet. |
| Whole connection | Produce consecutive, non-overlapping arrays in source order, including empty arrays when boundaries arrive without new values. |

With `b` boundaries before normal source completion and downstream still connected, emit `b + 1` arrays. The final array may be empty; errors and closure prevent its flush.

## Remembers

Each connection owns one current array and two subscriptions. On a boundary, replace the array before publishing it. Published arrays are no longer extended. Payload objects are retained by reference, not deeply copied.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Create an empty array; subscribe to the source first, then to the notifier. |
| Source next | Append to the current array. |
| Notifier next | Replace the current array with a fresh empty array and publish the previous one. |
| Notifier complete | Ignore completion as a boundary; continue collecting source values. |
| Notifier error | Error downstream; discard the unfinished buffer and release connections. |
| Source complete | Publish the current array, even if empty, then complete. |
| Source error | Error downstream without publishing the unfinished buffer. |
| Downstream close | Release both connections and the current buffer; emit no array or completion. |

## Time, cancellation, and sharing

- **Time:** Source and notifier determine grouping; no clock is introduced. Synchronous source values can arrive before the notifier is connected.
- **Cancellation:** Closing releases connections and unpublished values. It does not undo published batches or necessarily stop a shared producer.
- **Sharing:** No sharing is introduced. Separate downstream connections maintain separate arrays, including with an existing shared source.

## Worked trace

Illustrative cold source and cold notifier, connected at `t = 0`. Milliseconds are relative to connection. Source values are `a@10`, `b@20`, `c@40`; the boundary emits at `30`; the source completes at `50`.

| Time | Event | Current array after event | Output |
| --- | --- | --- | --- |
| 0 | Connect | `[]` | None |
| 10 | Source `a` | `[a]` | None |
| 20 | Source `b` | `[a, b]` | None |
| 30 | Boundary next | `[]` | `[a, b]` |
| 40 | Source `c` | `[c]` | None |
| 50 | Source complete | Released | `[c]`, then complete |

## Verification obligations

- An early boundary publishes an empty array.
- Normal empty-source completion publishes `[]` and completes.
- Notifier completion does not flush or terminate the output.
- Source/notifier error and downstream closure do not flush pending values.
- A synchronous source is connected before the notifier.

## Common confusion

`buffer` stores values for later array delivery. `window` publishes a live inner stream whose connected observers receive values as they arrive. Keeping a window reference for later does not preserve its past values. Also, notifier completion is not a substitute for notifier `next`.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/buffer)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/buffer.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
