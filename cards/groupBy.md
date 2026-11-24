# groupBy

```text
Operator:       groupBy
Stream shape:   T* → Group<K, T>*
State:          Map<K, LiveGroup<T>> × Nat × Bool
Initial state:  (Empty map, 0 active group subscriptions, false teardown requested)
Value step:     (S × T) → (S × Action[]) — create/publish key’s group if absent, then route value
Ownership:      One grouping execution per outer subscription; subscribed groups can retain its source
Lifecycle:      Source completion/error reaches groups and outer; outer cancellation defers source closure while groups remain
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** groupBy(keySelector), default Subject connector; no element or duration option · **Format:** Compact

> Publish one live keyed group for each new key and route subsequent values to the matching group.

## Notation and scope

Group<K, T> is T* with a key: K property. LiveGroup<T> is the backing Subject for a key. Map uses JavaScript Map key equality. Nat counts active group subscriptions (not distinct keys); Bool records an attempted outer teardown.

Publish a newly created group before sending its first value so an immediate inner subscriber can receive that value. Default groups do not replay. Key-selector failures and source errors error all groups and the outer result.

Closing the outer subscription does not sever upstream while group subscribers still depend on it. Once the outer has closed and the last group subscription closes, release upstream. Separate outer executions do not share a group map.

No scheduler is introduced. Element selectors, group-duration policies, and custom connectors require additional state/rules and are outside this compact scope.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/groupBy.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
