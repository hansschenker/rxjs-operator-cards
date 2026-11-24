# publishReplay

```text
Operator:       publishReplay
Stream shape:   T* → Connectable<T>
State:          T[] × Status × ObserverId[] × Option<InnerId>
Initial state:  ([], Open, [], None)
Value step:     (S × T) → (S × Action[]) — append, keep newest n values, and deliver to current observers
Ownership:      One fixed ReplaySubject, cache, and source connection per returned connectable
Lifecycle:      Source terminals stop retained hub; late observers replay then receive terminal; disconnect is silent
```

**Family:** Multicasting compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `publishReplay(n)`; positive integer buffer size, infinite time window, no selector · **Format:** Compact

> Current observers share live values, and later observers first receive the retained recent history.

## Notation and scope

- `Connectable<T>` names the runtime Observable view with explicit `connect()`. `Status = Open | Done | Failed(error)`; state also contains the retained FIFO, observer identities, and source connection.
- A subscription synchronously replays the retained values before following live delivery. Observing alone does not connect upstream; `connect()` starts the shared source subscription.
- The fixed ReplaySubject is never replaced. Both completion and error retain the cache: a late observer receives the retained values and then the stored terminal notification.
- Observer cancellation detaches it. Manual disconnect leaves the cache and hub intact for reconnection; reconnecting does not revive a stopped hub.
- This deprecated API is available from `rxjs/operators`. Default unlimited storage, selector overloads, and finite time windows are outside this bounded-count scope; a time-window variant uses its timestamp provider for expiry, not replay scheduling.
- Modern `connectable` uses a ReplaySubject connector with `resetOnDisconnect: false` for this retained-hub policy. No scheduler is introduced; [refCount](refCount.md) manages connection lifetime without clearing this fixed cache.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/publishReplay.ts)
- [ReplaySubject implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/ReplaySubject.ts)
- [Connectable implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/ConnectableObservable.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
