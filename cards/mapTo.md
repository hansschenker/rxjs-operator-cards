# mapTo

```text
Operator:       mapTo
Stream shape:   T* → R*
State:          Unit
Initial state:  ()
Value step:     (Unit × T) → (Unit × R) — emit the fixed value
Ownership:      No semantic value memory; each subscription owns its source connection
Lifecycle:      Forward completion/error; cancellation closes silently
```

**Family:** Compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `mapTo(value)`; fixed output `value: R` · **Format:** Compact

> Every source value becomes the same supplied output value at the same delivery point.

## Notation and scope

- `Unit` means no remembered source data. The fixed `value` is an operator argument, not an accumulator; the delegated map index is unused.
- The source payload is ignored. An object-valued replacement is the same reference on every emission and across uses of that configured operator; it is not cloned.
- This deprecated operator delegates to [map](map.md) with a constant projection. Its compatibility import is available from `rxjs/operators`.
- No scheduler or sharing is added. Source completion and error forward without emitting an extra replacement.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/mapTo.ts)
- [Delegated map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/map.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
