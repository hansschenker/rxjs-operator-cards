# skipLast

```text
Operator:       skipLast
Stream shape:   T* → T*
State:          T[] — bounded queue of the newest n values
Initial state:  []
Value step:     (T[] × T) → (T[] × Option<T>) — release oldest once n newer positions are available
Ownership:      One state per operator subscription
Lifecycle:      Completion discards the tail then completes; error forwards; cancellation discards and closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skipLast(n), with a nonnegative integer n · **Format:** Compact

> Hold back n values so that the final n never reach downstream.

## Notation and scope

The queue is a behavioral representation of the implementation’s ring buffer. For n > 0, append each value; once more than n values are represented, forward and remove the oldest.

For n = 0, mirror the source directly. The delay is measured in subsequent source arrivals, not elapsed time; there is no scheduler or sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skipLast.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
