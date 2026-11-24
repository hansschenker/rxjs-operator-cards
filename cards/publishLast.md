# publishLast

```text
Operator:       publishLast
Stream shape:   T* → Connectable<T>
State:          Option<T> × Status × ObserverId[] × Option<InnerId>
Initial state:  (None, Open, [], None)
Value step:     (S × T) → (S × Action[]) — remember the latest value; no delivery yet
Ownership:      One fixed AsyncSubject and source connection per returned connectable
Lifecycle:      Complete emits remembered last value then completes; error emits only error; disconnect is silent
```

**Family:** Multicasting compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `publishLast()` · **Format:** Compact

> Connected source values are remembered, and the last one is delivered only when the source completes successfully.

## Notation and scope

- `Connectable<T>` is an Observable with explicit `connect()`. `Status = Open | Done | Failed(error)`; the remaining state is the optional last value, observer identities, and source connection.
- Subscribing alone does not connect the source. Source next updates the cache without output; source completion delivers the cached value if present and then completion.
- The fixed AsyncSubject retains successful completion: late observers receive the last value if one existed, then completion. An error gives current and late observers only the error.
- Observer cancellation detaches that observer. Manual disconnect does not complete the hub or clear its cache; reconnection reuses both, while a stopped hub cannot be revived.
- This deprecated API is available from `rxjs/operators` and has no selector overload. Modern `connectable` uses an AsyncSubject connector with `resetOnDisconnect: false` for this retained-hub policy. No scheduler is added.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/publishLast.ts)
- [AsyncSubject implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/AsyncSubject.ts)
- [Connectable implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/ConnectableObservable.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
