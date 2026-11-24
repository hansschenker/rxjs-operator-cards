# concat

```text
Operator:       concat
Stream shape:   A* → (A | B)*
State:          Phase
Initial state:  Source
Value step:     (Phase × (A | B)) → (Phase × (A | B)) — forward a value from the active input
Ownership:      One phase and active input connection per operator subscription
Lifecycle:      Source complete opens other; other complete completes result; errors or cancellation close the active input
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `concat(other)`; one Observable `B*`, no scheduler · **Format:** Compact

> Source values flow first; the supplied stream starts only after the source completes normally.

## Notation and scope

- `Phase = Source | Other`. The fixed `other: B*` is part of the operator arguments; it is not connected while `Phase = Source`.
- The value step describes only the current input. On source completion, change phase and subscribe to `other`; its normal completion ends the result.
- Source error prevents the later subscription. Cancellation is silent, closes the active connection, and never starts remaining work.
- This is deprecated pipeable `concat` from `rxjs/operators`; the root `rxjs` creation function is a different entry point. Prefer [concatWith](concatWith.md). The scheduler overload is omitted.
- No sharing or default scheduling is added. A non-completing source prevents the later stream from being connected.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/concat.ts)
- [Sequential flattening](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/concatAll.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
