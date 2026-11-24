# connect

```text
Operator:       connect
Stream shape:   T* → R*
State:          SubjectId × Option<SourceId> × Option<InnerId>
Initial state:  (fresh subject, None, None)
Value step:     (S × T) → (S × Action[]) — multicast into the selector's shared input
Ownership:      One hub and source/result pair per result subscription; sharing is local to the selector
Lifecycle:      Selector result controls output termination; source terminals reach hub; output closure releases owned source
```

**Family:** Scoped sharing · **Reference:** RxJS 7.8.2 · **Scope:** connect(selector) with default Subject connector and Observable result · **Format:** Compact

> Give a selector a shared view of the source so its internal branches can use one source execution for each result subscription.

## Notation and scope

- The state identifies the fresh hub, source subscription, and selector-result subscription. The selector result is subscribed before the source; its values of type `R` form the output.

- Separate subscriptions to the returned Observable create separate hubs and source executions. Sharing is inside each selector invocation, rather than across those result subscriptions.

- Source next/error/complete goes to the hub; the selected dataflow determines the result's notifications. No scheduler is imposed. If the selector result ends synchronously, source setup is still called, then its subscription is disposed when attached to the already-closed output.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/connect.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
