# publish

```text
Operator:       publish
Stream shape:   T* → Connectable<T>
State:          Status × ObserverId[] × Option<InnerId>
Initial state:  (Open, [], None)
Value step:     (S × T) → (S × Action[]) — deliver to current observers while hub is Open
Ownership:      One fixed Subject per returned connectable; observers share its source connection
Lifecycle:      Source terminals stop the retained hub; observer cancellation detaches; disconnect closes source silently
```

**Family:** Multicasting compatibility · **Reference:** RxJS 7.8.2 · **Scope:** `publish()`; no selector · **Format:** Compact

> An explicit connection shares live source values among the observers currently attached to one fixed Subject.

## Notation and scope

- `Connectable<T>` is an Observable with explicit `connect()`. `Status = Open | Done | Failed(error)`; `ObserverId[]` is the hub's observer registry, and `InnerId` identifies the source connection.
- Subscribing registers an observer but does not start the source. `connect()` opens one shared upstream subscription; active repeated connects return that connection.
- The plain Subject retains no value history. Source completion/error stops it permanently, so late observers receive only the terminal notification; the fixed Subject is never replaced.
- Manual disconnect leaves the Subject open and its observers attached; reconnecting starts another upstream subscription using that same hub. A stopped hub cannot be revived by reconnecting.
- This deprecated API is available from `rxjs/operators`. Modern `connectable` with a Subject connector and `resetOnDisconnect: false` preserves this retained-hub policy. The selector overload uses scoped [connect](connect.md) and is omitted; no scheduler is added.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/publish.ts)
- [Multicast implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/multicast.ts)
- [Connectable implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/observable/ConnectableObservable.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
