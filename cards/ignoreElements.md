# ignoreElements

```text
Operator:       ignoreElements
Stream shape:   T* → Never*
State:          Unit — no payload memory
Initial state:  ()
Value step:     (Unit × T) → (Unit × Action[]) — no actions
Ownership:      One source subscription per operator subscription
Lifecycle:      Forward completion/error; cancellation closes upstream silently
```

**Family:** Value suppression · **Reference:** RxJS 7.8.2 · **Scope:** ignoreElements() · **Format:** Compact

> Suppress every source value while preserving its normal or error termination.

## Notation and scope

- `Never` is an uninhabited payload type: `Never*` has no `next` values, while still supporting completion, error, and cancellation. It is not the specific `NEVER` Observable.

- Source values produce no downstream action, but the source continues running until it terminates or the consumer closes the connection.

- Source timing and effects remain in force. The operator adds neither a scheduler nor sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/ignoreElements.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
