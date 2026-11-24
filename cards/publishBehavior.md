# publishBehavior

```text
Operator:       publishBehavior
Stream shape:   T* → Connectable<T>
State:          T × Status × ObserverId[] × Option<InnerId>
Initial state:  (seed, Open, [], None)
Value step:     (S × T) → (S × Action[]) — replace current value and deliver to observers while Open
Ownership:      One fixed BehaviorSubject and source connection per returned connectable
Lifecycle:      Source terminals stop retained hub; late observers receive terminal only; disconnect closes source silently
```

**Family:** Multicasting compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `publishBehavior(seed)` · **Format:** Compact

> Observers share the latest value, beginning with the seed, while an explicit connection controls source execution.

## Notation and scope

- `Connectable<T>` is an Observable with explicit `connect()`. `Status = Open | Done | Failed(error)`; state also contains the latest value, observer identities, and optional source connection.
- While the hub is open, subscribing immediately receives its current value even before `connect()` is called. Each source next replaces that value and delivers it to current observers.
- The fixed BehaviorSubject is never replaced. After completion or error, a late observer receives only that terminal notification, **not** the stored value followed by the terminal.
- Canceling an observer detaches it. Manual disconnect leaves the hub and cached value intact; reconnecting reuses them, without reviving a stopped hub.
- This deprecated API is available from `rxjs/operators` and has no selector overload. Modern `connectable` uses a BehaviorSubject connector with `resetOnDisconnect: false` for this retained-hub policy. No scheduler is added, and the seed is not cloned.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/publishBehavior.ts)
- [BehaviorSubject implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/BehaviorSubject.ts)
- [Connectable implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/ConnectableObservable.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
