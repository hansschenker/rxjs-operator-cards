# materialize

```text
Operator:       materialize
Stream shape:   T* → Notification<T>*
State:          Unit — no payload history
Initial state:  ()
Value step:     (Unit × T) → (Unit × Notification<T>) — emit Next(value) as data
Ownership:      One notification conversion per operator subscription
Lifecycle:      Source complete/error becomes a value then output completion; cancellation adds no notification value
```

**Family:** Notification representation · **Reference:** RxJS 7.8.2 · **Scope:** All three source notification kinds · **Format:** Compact

> Represent values, completion, and errors as ordinary notification payloads that can flow through other operators.

## Notation and scope

- `Notification<T>` is the tagged choice `Next(T) | Error(unknown) | Complete`. A source value becomes a `Next` notification carried by downstream `next`.

- Source completion emits a `Complete` payload and then completes the output. Source error emits an `Error` payload and then completes the output; that source error is no longer delivered through the output error channel.

- Cancellation closes upstream without synthesizing a payload. Conversion follows source timing, introduces no scheduler or sharing, and does not continue the terminated source.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/materialize.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
