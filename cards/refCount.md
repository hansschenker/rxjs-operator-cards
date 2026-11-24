# refCount

```text
Operator:       refCount
Stream shape:   Connectable<T> → T*
State:          Nat × Option<InnerId> × Option<InnerId>
Initial state:  (existingCount, existingConnection, None); fresh connectable: (0, None, None)
Value step:     (S × T) → (S × T) — forward a value from the shared hub
Ownership:      Count and connection belong to connectable; each observer owns a local connection reference
Lifecycle:      Forward source terminals; last counted observer disconnects matching connection; cancellation is silent
```

**Family:** Multicasting compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `refCount()` applied to a legacy ConnectableObservable · **Format:** Compact

> The first live observer starts the shared connection, and the last counted observer leaving disconnects it.

## Notation and scope

- `Connectable<T>` is the legacy Observable with `connect()` and shared count/connection fields. `S` contains that shared count, shared connection identity, and this observer's optional connection identity.
- On subscription, increment the shared count and subscribe to the hub before connecting. Connect only if that observation remains open, so an already-stopped hub does not trigger a new run.
- An observer leaving reduces the count when applicable. At zero, disconnect only the current matching shared connection; the local identity guard also handles synchronous unsubscription during connect.
- Source termination forwards through the hub and clears the connectable's connection bookkeeping. The hub's factory and Subject type determine retention or reset on later observations.
- This deprecated operator is available from `rxjs/operators`; modern [share](share.md) makes connection and reset choices explicit. RefCount supplies no independent replay/reset policy or scheduler; applying it to [publishReplay](publishReplay.md) does not clear that fixed cache at zero observers.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/refCount.ts)
- [Connectable implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/ConnectableObservable.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
