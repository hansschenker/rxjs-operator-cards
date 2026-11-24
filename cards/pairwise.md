# pairwise

```text
Operator:       pairwise
Stream shape:   T* → (T × T)*
State:          Option<T>
Initial state:  None
Value step:     (Option<T> × T) → (Option<T> × Option<T × T>)
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; cancellation closes silently
```

**Family:** Sequence analysis · **Reference:** RxJS 7.8.2 · **Scope:** pairwise() · **Format:** Compact

> Remember each value and, from the second arrival onward, emit the previous/current pair.

## Notation and scope

Option<T> remembers the previous received value: None initially, Some(value) after an arrival. The first value changes memory without emitting; subsequent values emit one ordered pair.

Actual null and undefined payloads remain values inside Some. Pair delivery follows the current source arrival with no scheduler or introduced sharing. This compact value model assumes ordinary non-reentrant processing; it is not a replacement for RxJS execution machinery.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/pairwise.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
