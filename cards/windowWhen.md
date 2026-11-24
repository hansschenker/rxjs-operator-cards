# windowWhen

```text
Operator:       windowWhen
Stream shape:   T* → (T*)*
State:          LiveWindow<T> × Option<InnerId> — current inner and closing connection
Initial state:  (Fresh published window, first closing connection)
Value step:     (S × T) → (S × Action[]) — deliver to the current window
Ownership:      One window sequence per outer subscription; owns source and selected closing stream
Lifecycle:      Completion/error reaches inner and outer; cancellation closes owned work without completing held inner
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** windowWhen(closingSelector), with Observable closing streams · **Format:** Compact

> Publish a live window and replace it whenever its selected closing stream sends a value or completes.

## Notation and scope

LiveWindow<T> is the current Subject-backed stream; InnerId identifies the closer. Setup publishes the initial window, selects/subscribes to its closer, then connects the source.

Closing next OR completion completes the old window and starts a new window/closer. This differs from bufferWhen, where closing completion is ignored. Closing/selector errors terminate both current window and outer result.

Outer cancellation releases the source and closer without completing independently held inner observers. Windows do not replay and no scheduler is introduced. Repeated synchronous closing completion can repeatedly reopen during setup.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/windowWhen.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
