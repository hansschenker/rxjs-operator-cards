# exhaustMap

**Family:** Flattening · **Reference:** RxJS 7.8.2 · **Scope:** `exhaustMap(project)`; no deprecated `resultSelector`.

> An accepted source value opens an inner stream; new values are ignored until it completes, preventing overlapping work.

## Type shape

```text
exhaustMap :: (T → R*) → T* → R*
```

The projection describes an inner Observable for an accepted value. This explanatory signature omits its index and simplifies `ObservableInput` to Observable. Ignored values never call the projection.

## Intuition

A refresh button is pressed repeatedly. Keep the current refresh connected and ignore presses until it finishes. The next press after completion can start another refresh.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | When idle, reserve the inner slot, project, and connect. When busy, ignore the value without projection or queueing. |
| Whole connection | `T* → R*`; each admitted inner may emit zero, one, or many values while later source values are ignored. |

One output per input is not guaranteed. A never-completing inner keeps the connection busy, even after it stops emitting.

## Remembers

Each connection owns the current inner subscription, source-completed flag, and admission index. Only projection calls advance that index. There is no waiting queue.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Start idle and connect to the source. |
| Source next while idle | Reserve the inner subscription before calling the projection, then connect it to the returned Observable. |
| Source next while busy | Ignore this value; leave the current inner unchanged. |
| Current inner next | Forward its value; remain busy. |
| Current inner complete | Become idle; complete downstream if the source has completed. |
| Source complete | Remember completion; wait for the active inner, or complete immediately when idle. |
| Source error | Error downstream and close the active inner. |
| Projection or inner error | Error downstream and close owned connections. |
| Downstream close | Close source and inner; stop delivery. |

## Time, cancellation, and sharing

- **Time:** Sources and their schedulers supply timing. The busy period ends on inner completion, not on its first value or a fixed timeout.
- **Cancellation:** New source values do not cancel active work. Closing the result runs owned teardown without sending `complete` or reversing effects; stopping an external producer depends on its teardown.
- **Sharing:** Busy state is per downstream connection. Sharing the source alone does not create a common admission gate.

## Worked trace

Illustrative milliseconds are measured from the source connection. A cold source emits A at 0, B at 20, and completes at 30. Each cold inner emits its label 50 ms after its own connection, then immediately completes. Within a row, `next` precedes `complete`.

| Time | Event | Output and state |
| --- | --- | --- |
| 0 | Source A | Project and connect A; become busy. |
| 20 | Source B | Ignore B; its projection never runs. |
| 30 | Source complete | Wait for A. |
| 50 | A next, complete | Emit A, then complete; no B is waiting. |

## Verification obligations

- Check the trace and that the projection receives only A.
- Check that inner `next` leaves the gate busy until inner completion.
- Check that a later source value is admitted after completion.
- Check empty-inner completion, inner error, and downstream closure.

## Common confusion

Ignoring is not queueing: B is permanently skipped in this trace. `map(project)` followed by `exhaustAll()` calls the projection even for inputs whose inners are subsequently ignored; it may create unwanted work and passes different indices to the projection.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/exhaustMap)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/exhaustMap.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
