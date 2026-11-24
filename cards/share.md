# share

```text
Operator:       share
Stream shape:   T* → T*
State:          SharedState<T> — subject, observers, upstream
Initial state:  { subject: None, observers: [], upstream: None }
Value step:     (S × T) → (S × Action[]) — multicast to current observers
Ownership:      One shared state per resulting Observable; observers share its active upstream connection
Lifecycle:      Source terminal resets then notifies; last observer leaving unfinished source closes/resets silently
```

**Family:** Sharing · **Reference:** RxJS 7.8.2 · **Scope:** share() defaults: Subject connector; all three reset policies true · **Format:** Compact

> Let overlapping observers share one active source connection, with a fresh connection after a reset.

## Notation and scope

- `SharedState<T>` contains `subject: Option<SubjectId>`, `observers: ObserverId[]`, and `upstream: Option<SourceId>`. These IDs represent the current multicast subject, registered observers, and source subscription; observer count supplies reference counting.

- The first observer opens upstream; later concurrent observers join without replay. Source error/completion resets the shared state before notifying the observers of the previous subject. A later observer can start fresh.

- When the last observer leaves an unfinished source, upstream is closed and state resets. Source timing is preserved. Custom connectors, delayed resets, and disabled reset policies require different state/lifecycle summaries.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/share.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
