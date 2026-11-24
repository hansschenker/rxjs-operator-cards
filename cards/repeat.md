# repeat

```text
Operator:       repeat
Stream shape:   T* → T*
State:          Nat — completed source runs
Initial state:  0
Value step:     (Nat × T) → (Nat × T) — forward value without changing completed-run count
Ownership:      One repeat counter and current source subscription per operator subscription
Lifecycle:      Completion repeats until total count; errors forward immediately; count 0 skips source; cancellation stops repeats
```

**Family:** Completion resubscription · **Reference:** RxJS 7.8.2 · **Scope:** Nonnegative integer total count n, or default Infinity; no delay config · **Format:** Compact

> Reconnect to the source after normal completion to repeat its sequence the requested total number of times.

## Notation and scope

- `n` counts total source runs, including the initial run; `repeat(1)` runs once and `repeat(0)` completes without subscribing upstream. The counter advances on each normal completion.

- Errors terminate immediately and are not repeat triggers. A source that never completes cannot begin a later run. Earlier values are retained in the delivered history, not retracted.

- Immediate repetition introduces no scheduler or sharing. The delay configuration adds a waiting phase: notifier next begins another run, empty completion completes the output, and notifier error terminates.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/repeat.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
