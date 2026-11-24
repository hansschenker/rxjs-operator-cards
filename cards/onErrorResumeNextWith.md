# onErrorResumeNextWith

```text
Operator:       onErrorResumeNextWith
Stream shape:   T* → (T | R)*
State:          Nat — current source position
Initial state:  0 — original source first
Value step:     (Nat × (T | R)) → (Nat × (T | R)) — forward current source values
Ownership:      One sequential source progression per operator subscription
Lifecycle:      Each complete/error advances; final exhaustion completes; cancellation closes without forwarding source errors
```

**Family:** Error-tolerant concatenation · **Reference:** RxJS 7.8.2 · **Scope:** Observable arguments following the original source · **Format:** Compact

> Visit the source and each supplied continuation in order, advancing after either completion or error.

## Notation and scope

- `R` is the union of the continuation payload types. The index identifies the current position in the fixed list beginning with the original source.

- A source error is swallowed and advances to the next source just as completion does. After the last source ends, the output completes; a source that never ends prevents later sources from starting.

- No scheduler or sharing is introduced. Cancellation closes the active subscription and prevents later value delivery. Continuations are fixed arguments, not selected from the error.

## Alias: onErrorResumeNext

```text
Operator:       onErrorResumeNext
Stream shape:   T* → (T | R)*
State:          Nat — current source position
Initial state:  0 — original source first
Value step:     (Nat × (T | R)) → (Nat × (T | R)) — forward current source values
Ownership:      One sequential source progression per operator subscription
Lifecycle:      Each complete/error advances; final exhaustion completes; cancellation closes without forwarding source errors
```

The deprecated pipeable `onErrorResumeNext` export from `rxjs/operators` aliases this operator and has the same policy. The similarly named creation function exported from root `rxjs` is a different API. Prefer the pipeable name `onErrorResumeNextWith` for this form.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/onErrorResumeNextWith.ts)
- [Sequential continuation helper](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/onErrorResumeNext.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
