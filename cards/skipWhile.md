# skipWhile

```text
Operator:       skipWhile
Stream shape:   T* → T*
State:          Bool × Nat — forwarding gate and predicate index
Initial state:  (false, 0)
Value step:     (S × T) → (S × Option<T>) — open gate on first rejection; then always forward
Ownership:      One state per operator subscription
Lifecycle:      Forward source completion/error; predicate errors terminate; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skipWhile(predicate) · **Format:** Compact

> Skip the initial accepted run, then forward the first rejected value and every later value.

## Notation and scope

Bool is false while skipping and true after the predicate first returns false. Nat counts predicate calls; once the gate opens, the predicate is no longer called and its index stops advancing.

An empty source or an entirely accepted finite source completes without values. Timing follows the source; there is no notifier, scheduler, or shared state.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skipWhile.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
