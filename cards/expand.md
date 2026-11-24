# expand

```text
Operator:       expand
Stream shape:   T* → T*
State:          InnerId[] × Nat × Bool
Initial state:  ([], 0, false)
Value step:     (S × T) → (S × Action[]) — emit the value, then project and connect its descendants
Ownership:      One recursive connection set and projection index per operator subscription
Lifecycle:      Source complete waits for every descendant; any error closes all; cancellation closes silently
```

**Family:** Recursive expansion · **Reference:** RxJS 7.8.2 · **Scope:** `expand(project)`; homogeneous Observable results, unlimited concurrency, no scheduler · **Format:** Compact

> Each source or descendant value is emitted and also used to describe another stream of descendants.

## Notation and scope

- `S` contains active descendant connections, the next projection index, and the original-source-completed flag. The Value step applies to both original-source and descendant next values.
- For each admitted value, forward it before calling `project(value, index)` and connecting the returned stream. The index counts projection calls across the whole expansion.
- A descendant completion removes its connection. Final completion requires the original source to have completed and all recursive work to have finished.
- Finite concurrency adds a FIFO of not-yet-admitted values and can delay their emission as well as projection; that overload and the deprecated scheduler argument are outside this scope.
- The default adds no scheduler or sharing. Synchronous descendants can expand synchronously; time and termination depend on the supplied streams.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/expand.ts)
- [Recursive merge coordination](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mergeInternals.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
