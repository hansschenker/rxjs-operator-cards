# distinctUntilChanged

```text
Operator:       distinctUntilChanged
Stream shape:   T* → T*
State:          Option<K> — key of the last emitted value
Initial state:  None
Value step:     (Option<K> × T) → (Option<K> × Option<T>) — emit first or unequal key
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; selector/comparator errors terminate; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** distinctUntilChanged(comparator?, keySelector?), identity key selector by default · **Format:** Detailed

> Compare each arrival with the last emitted key and forward it only when the comparator reports a change.

## Type shape

```text
distinctUntilChanged :: ((K × K) → Bool) → (T → K) → T* → T*
```

This explanatory signature makes both choices visible. The default selector is identity, making `K = T`; the default comparator is `===`. The comparator receives the previous accepted key and current candidate key. Returning `true` suppresses the candidate; returning `false` admits its original payload.

## Intuition

Readings arrive frequently, but an application may want to publish only meaningful changes. Compare each new reading against the last reading actually published. The supplied comparator defines meaningful sameness. `distinctUntilChanged` preserves that accepted reference point while suppressing candidates considered equivalent to it.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Select its key; accept the first value or one whose key compares unequal to the remembered key. |
| Whole connection | `T* → T*`; each arrival produces zero or one output, while the full connection may produce many. |

The first source value is accepted if its selector returns normally. A suppressed arrival does not replace the remembered key. There is no output added on completion, so an empty source completes empty.

## Remembers

`Option<K>` is `None` before the first accepted value and `Some(key)` afterward. `Some(undefined)` and `Some(null)` are valid remembered keys, distinct from absence. The selector runs for every processed source value, including the first; the comparator runs only after a key has been accepted.

Acceptance updates remembered state before forwarding the payload, which also matters for reentrant notifications. A primitive selected key is retained as its selected value; a selected object is retained by reference, without cloning. State belongs to each downstream connection.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Begin with no accepted key and connect to the source. |
| First source next | Run the selector, store its key, and emit the original value without comparison. |
| Later source next, comparator true | Suppress the value and keep the last accepted key. |
| Later source next, comparator false | Store the new key before emitting the original value. |
| Source complete | Forward completion without an additional value. |
| Source error | Forward the error and close the connection. |
| Selector or comparator throws | Error downstream and close upstream. |
| Downstream close | Close upstream silently. |

## Time, cancellation, and sharing

- **Time:** Comparison follows source delivery synchronously; no timer, clock, or scheduler is added.
- **Cancellation:** Suppression leaves upstream connected. Only termination or downstream closure releases this connection; it neither sends an extra completion nor undoes earlier effects.
- **Sharing:** No sharing is introduced. Separate connections maintain separate accepted keys, even when observing the same live source. Sharing after this operator shares its selection history.

## Worked trace

Illustrative times are virtual milliseconds from connection to a cold source. Use identity keys and `compare(previous, current) = abs(current - previous) < 3`. This deliberately uses a tolerance rule that is not transitive.

| Time | Event | Output and remembered key |
| --- | --- | --- |
| 10 | Source 10 | Emit 10; remember 10. |
| 20 | Source 12 | Suppress; remember 10. |
| 30 | Source 14 | Emit 14: compare against 10, not 12. |
| 40 | Source 13 | Suppress; remember 14. |
| 50 | Source 18 | Emit 18; remember 18. |
| 60 | Source complete | Complete without another value. |

## Verification obligations

- Check the trace and comparator pairs, especially `(10, 14)`.
- Check that the selector runs on every input, while the first input skips comparison.
- Check `undefined` as a key, selector/comparator failures, and independent state.
- Check accepted state is updated before reentrant source delivery.

## Common confusion

The default comparison is not deep equality or Set membership. Repeated `NaN` values each pass because `NaN === NaN` is false; signed zeros compare equal. Distinct object instances pass unless a selector or comparator supplies another rule. Also, `A, B, A` can emit all three: remembering the last accepted key is different from remembering every key ever seen. A custom comparator need not be an equivalence relation, making the accepted-reference rule especially significant.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/distinctUntilChanged)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinctUntilChanged.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
