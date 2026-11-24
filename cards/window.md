# window

```text
Operator:       window
Stream shape:   T* → (T*)*
State:          LiveWindow<T> — current live inner
Initial state:  Fresh window, published before source subscription
Value step:     (LiveWindow<T> × T) → (LiveWindow<T> × Action[]) — deliver to inner observers
Ownership:      One window and source/boundary subscription pair per outer subscription
Lifecycle:      Source complete finishes inner/outer; errors fail both; outer cancellation disposes inner without completion
```

**Family:** Notifier grouping · **Reference:** RxJS 7.8.2 · **Scope:** Observable source and Observable boundary notifier

> Publish a live inner stream immediately, route arriving values into it, and replace it on each boundary value.

## Type shape

```text
window :: N* → T* → (T*)*
```

The outer stream carries live, non-replaying inner Observables requiring their own subscriptions. Boundary payloads are ignored. Other `ObservableInput` boundary forms are omitted.

## Intuition

Divide readings into sessions. Publish each session stream immediately so observers can process arriving readings. A boundary finishes that session and opens another. An unattended session stores no readings for future observers.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Push into the current inner; emit no outer value. |
| Whole connection | Emit an initial window, then another for every boundary; consecutive windows may be empty. |

An outer connection handling `b` boundaries produces `b + 1` windows. Receiving their payloads requires timely inner subscriptions.

## Remembers

`LiveWindow<T>` in the compact header names the current Subject-backed window. A source-value action delivers to that window's observers; it does not publish another outer value.

Each outer connection owns a current Subject-backed window and two subscriptions. The Subject multicasts without retaining payload history. Inner subscriptions have separate lifetimes; closing one does not close the outer connection.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Publish the initial window; subscribe to the source, then the boundary notifier. |
| Source next | Deliver through the current window to its connected observers. |
| Boundary next | Complete the current window; publish its replacement. |
| Boundary complete | Ignore as a boundary; keep the current window active. |
| Boundary error | Error the current window and the outer stream. |
| Source complete | Complete the current window, then complete the outer stream. |
| Source error | Error the current window and the outer stream. |
| Downstream outer close | Release source and boundary connections; unsubscribe the current Subject. Do not promise completion of its observers. |

## Time, cancellation, and sharing

- **Time:** Source and boundaries supply timing; no scheduler is added. The initial window appears before source subscription, permitting an inner subscription before synchronous values arrive.
- **Cancellation:** Outer teardown unsubscribes the current Subject without completing it. Connected inner observers receive no terminal notification and need their own cleanup. A later subscription to that disposed window throws `ObjectUnsubscribedError`.
- **Sharing:** Windows multicast without replay; separate outer subscriptions have separate grouping state. Inner observers do not keep the source connection alive independently.

## Worked trace

Illustrative cold source and cold boundary notifier connect at `t = 0`; times are milliseconds relative to connection. Subscribe to each inner immediately when the outer publishes it.

| Time | Event | Outer output | Inner output |
| --- | --- | --- | --- |
| 0 | Connect | Window `W1` | None |
| 10 | Source `a` | None | `W1: a` |
| 20 | Source `b` | None | `W1: b` |
| 30 | Boundary next | Window `W2` | `W1` completes before `W2` opens |
| 40 | Source `c` | None | `W2: c` |
| 50 | Source complete | Complete | `W2` completes first |

## Verification obligations

- The initial window is published even before any source value.
- A late inner observer misses earlier values; multiple current observers share later ones.
- Boundary completion leaves the current window active.
- Errors reach both current inner and outer observers.
- Outer unsubscription does not manufacture inner completion.

## Common confusion

Use `buffer` for saved batches, or collect each window while active. Receiving a window does not subscribe. Normal source completion completes the current window; outer unsubscription does not.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/window)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/window.ts)
- [RxJS 7.8.2 Subject lifecycle](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/Subject.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
