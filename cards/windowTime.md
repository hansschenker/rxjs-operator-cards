# windowTime

```text
Operator:       windowTime
Stream shape:   T* → (T*)*
State:          LiveWindow<T> × TimerId — current live inner and closing task
Initial state:  (Fresh published window, first scheduled closing task)
Value step:     (S × T) → (S × Action[]) — deliver to current inner observers
Ownership:      One window sequence and scheduled work per outer subscription
Lifecycle:      Source completion/error reaches inner and outer; cancellation releases work without completing held inners
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** windowTime(span, scheduler?), positive span; no creation interval or size limit · **Format:** Compact

> Publish a live window immediately and replace it at each scheduled time boundary.

## Notation and scope

LiveWindow<T> is a Subject-backed stream with no replay; TimerId identifies its closing work. Closing a window completes it, then publishes and schedules the next window, including during otherwise silent periods.

The scheduler (asyncScheduler by default) supplies the clock. Windows are shared only by observers of that particular emitted inner; separate outer subscriptions have separate window sequences.

Outer cancellation cancels timers and the source connection without delivering an inner completion. Creation-interval overlaps/gaps and maximum-size closure are outside this scope.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/windowTime.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
