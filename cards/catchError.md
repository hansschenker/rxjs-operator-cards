# catchError

```text
Operator:       catchError
Stream shape:   T* → (T | R)*
State:          Source | Recovery — active connection phase
Initial state:  Source
Value step:     (Source × T) → (Source × T) — forward until source failure
Ownership:      One source/recovery progression per operator subscription
Lifecycle:      Source error selects recovery; recovery determines termination; selector/recovery errors forward; cancellation closes active work
```

**Family:** Error recovery · **Reference:** RxJS 7.8.2 · **Scope:** Selector returning an Observable<R> · **Format:** Compact

> Forward source values, then replace a failed source with the Observable chosen by a supplied selector.

## Notation and scope

- The selector receives the source error and the caught Observable. Normal source completion passes through without invoking it. Before subscribing to an independent recovery, the failed source is torn down, including for synchronous failure.

- Recovery values of type `R` pass directly to the output. Recovery failure is not automatically caught again by this operator; returning the provided caught Observable explicitly creates a resubscription loop.

- No scheduler or sharing is imposed. The source and selected recovery supply timing; cancellation closes whichever connection is active.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/catchError.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
