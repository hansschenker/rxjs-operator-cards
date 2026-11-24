# bufferToggle

```text
Operator:       bufferToggle
Stream shape:   T* → (T[])*
State:          BufferSlot<T>[] — open batches and their closing subscriptions
Initial state:  []
Value step:     (S × T) → (S × Action[]) — append to every open batch; no immediate output
Ownership:      One openings/source connection and set of owned closers per operator subscription
Lifecycle:      Source completion flushes open batches; any error forwards; cancellation discards and closes silently
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** bufferToggle(openings, closingSelector), with Observable inputs · **Format:** Compact

> Open a batch for each opening value and publish that batch on its closing notifier’s first value.

## Notation and scope

BufferSlot<T> contains values: T[] and its closing subscription identity. Multiple slots may overlap; a source value is copied into each open batch and is omitted if none is open.

Openings are subscribed before the source. Each opening calls closingSelector; closing next publishes and removes that batch, including an empty batch. Closing completion alone does not close the batch; openings completion stops future openings without ending the result.

Source, opening, closing, and selector errors terminate the result without flushing. Time comes from participating streams; this operator introduces no scheduler or sharing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/bufferToggle.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
