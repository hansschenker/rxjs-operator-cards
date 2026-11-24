# multicast

```text
Operator:       multicast
Stream shape:   T* → Connectable<T>
State:          Option<HubId> × Option<InnerId>
Initial state:  (None, None)
Value step:     (S × T) → (S × Action[]) — fan out through the current hub while connected
Ownership:      One cached hub and source connection per returned connectable; observers share them
Lifecycle:      Source terminals end current hub and reset connection; observer cancellation detaches; disconnect is silent
```

**Family:** Multicasting compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `multicast(subjectFactory)`; fresh plain Subject per factory call, no selector · **Format:** Compact

> Observers share a Subject-backed view, and an explicit connection controls when source values begin flowing.

## Notation and scope

- `Connectable<T>` is an Observable view with explicit `connect()`. `HubId` names an owned Subject with its observer registry and terminal state; `InnerId` names the source connection.
- The first observation or connection obtains the cached hub. Observing alone does not subscribe upstream; `connect()` creates one shared source subscription, and repeated connects reuse an active connection.
- Source completion/error forwards to that hub and clears the cached hub and connection. With this fresh factory, later observation or connection obtains a new Subject.
- Manual disconnection clears the cache without completing the old hub. Existing observers remain attached to that old hub; they are not moved to the newly created hub on reconnection.
- This deprecated API is available from `rxjs/operators`; modern `connectable` supports explicit connection ownership. Passing a fixed Subject reuses that Subject instead of creating fresh ones; selector overloads use scoped [connect](connect.md) and are outside this card.
- No scheduler is introduced. Canceling an observer only detaches it; explicitly disconnect the connection, or use [refCount](refCount.md), to manage upstream lifetime.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/multicast.ts)
- [Connectable implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/ConnectableObservable.ts)
- [Subject implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/Subject.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
