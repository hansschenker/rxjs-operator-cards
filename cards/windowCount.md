# windowCount

```text
Operator:       windowCount
Stream shape:   T* → (T*)*
State:          LiveWindow<T> × Nat — current inner and its value count
Initial state:  (Fresh published window, 0)
Value step:     (S × T) → (S × Action[]) — forward to window; at n, complete it and publish the next
Ownership:      One window sequence per outer subscription; inner observers share each live window
Lifecycle:      Source completion/error reaches current inner and outer; cancellation closes upstream without inner completion
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** windowCount(n), positive integer n and default nonoverlapping start interval · **Format:** Compact

> Publish live windows of n values, opening the next window as soon as the current one fills.

## Notation and scope

LiveWindow<T> is a Subject-backed inner stream; Nat counts values in the current window. The initial window is published during setup before source subscription.

After every nth source value, close the current window and immediately publish a fresh one. Consequently an empty source, or completion exactly at a window boundary, can leave an emitted empty window that completes.

Outer cancellation releases upstream but does not send completion to independently held inner observers in this implementation. Windows do not replay; there is no scheduler. The optional startWindowEvery overlap/gap policy is outside this scope.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/windowCount.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
