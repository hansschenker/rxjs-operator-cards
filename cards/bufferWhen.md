# bufferWhen

```text
Operator:       bufferWhen
Stream shape:   T* → (T[])*
State:          T[] × Option<InnerId> — current batch and closing connection
Initial state:  (Empty batch, first closing connection)
Value step:     (S × T) → (S × Action[]) — append; no immediate output
Ownership:      One state per operator subscription; owns source and current closing subscription
Lifecycle:      Completion flushes then completes; source/closing/selector errors forward; cancellation discards silently
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** bufferWhen(closingSelector), with Observable closing streams · **Format:** Compact

> Collect one batch until the selected closing stream sends a value, then publish it and select the next closer.

## Notation and scope

InnerId identifies a closing subscription. Setup creates the first empty batch and subscribes to its closer before subscribing to the source. Closing next ends that closer, emits the current batch (possibly empty), and starts another batch and closer.

Closing completion alone is ignored: the current batch remains open until source termination or another applicable event. Source completion emits the current batch, including an empty one.

Timing comes from source and closing streams; no scheduler or sharing is introduced. A selector that repeatedly returns a synchronous emitting closer can repeatedly reopen during setup.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/bufferWhen.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
