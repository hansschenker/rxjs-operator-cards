# finalize

```text
Operator:       finalize
Stream shape:   T* → T*
State:          Unit — no payload memory
Initial state:  ()
Value step:     (Unit × T) → (Unit × T)
Ownership:      One registered teardown callback per operator subscription
Lifecycle:      Forward source terminals; complete/error/cancellation run cleanup; cancellation sends no completion
```

**Family:** Resource finalization · **Reference:** RxJS 7.8.2 · **Scope:** Non-throwing teardown callback · **Format:** Compact

> Run supplied cleanup when this subscription ends, regardless of whether it completes, errors, or is canceled.

## Notation and scope

- Values pass through unchanged. The callback is registered as teardown, including when a synchronous source has already terminated before subscription setup returns.

- Cleanup is performed for this subscription and does not emit a value or replace its terminal event. A subscriber closing early also triggers cleanup.

- No scheduler or sharing is introduced. Callback failure is a teardown-error concern, outside this non-throwing scope; cleanup is not a catchError-style recovery path.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/finalize.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
