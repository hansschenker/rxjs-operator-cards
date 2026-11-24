# distinctUntilKeyChanged

```text
Operator:       distinctUntilKeyChanged
Stream shape:   T* → T*
State:          Option<T> — last emitted object reference
Initial state:  None
Value step:     (Option<T> × T) → (Option<T> × Option<T>) — compare the selected properties
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; property/comparator failures terminate; cancellation closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** distinctUntilKeyChanged(key, compare?), with key in T · **Format:** Detailed

> Forward the first object and subsequent objects whose selected property differs from the last emitted object’s property.

## Type shape

```text
distinctUntilKeyChanged :: Key → ((V × V) → Bool) → T* → T*
```

Here `Key` names a property of object type `T`, and `V = T[Key]` is its value type. This explanatory form makes the optional comparator explicit; its default is `===`. A supplied comparator receives the previous accepted object's property value and the current candidate's property value, not the whole objects.

## Intuition

Status records can contain a status plus changing metadata. Publish the first record, then publish another only when its status differs from the last accepted record's status. The entire accepted record flows onward, so its metadata remains available. The operator does not merge records or update the downstream view for suppressed metadata changes.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | Emit the first object; afterward compare the selected properties and emit only when comparison returns false. |
| Whole connection | `T* → T*`; an arrival produces zero or one output, and the full stream may contain arbitrarily many changes. |

The first object bypasses property comparison. A suppressed object does not replace the accepted reference. An empty source completes empty; there is no initial object supplied by this operator.

## Remembers

`Option<T>` stores the full last emitted object reference, initially `None`. On acceptance, it becomes `Some(currentObject)` before output delivery. On suppression, it remains unchanged.

RxJS 7.8.2 implements this through `distinctUntilChanged` with an identity selector and a comparator that reads `previous[key]` and `current[key]`. It therefore stores the object, not a snapshot of the selected property. Even the previous property is read again for each comparison. The first object is emitted without reading that property, so a property getter need not run until a subsequent arrival.

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | Begin without an accepted object and connect to the source. |
| First source next | Retain the object and emit it without property comparison. |
| Later source next, properties compare equal | Suppress the candidate and retain the previous accepted object. |
| Later source next, properties compare unequal | Retain the candidate before emitting it. |
| Source complete | Forward completion without an additional object. |
| Source error | Forward the error and close the connection. |
| Property access or comparator throws | Error downstream and close upstream. |
| Downstream close | Close upstream silently. |

## Time, cancellation, and sharing

- **Time:** Property reads and comparisons follow source notifications. No scheduling or time window is introduced.
- **Cancellation:** Suppressed records do not close upstream. Termination or downstream closure runs owned teardown; it does not reverse earlier effects or necessarily stop a shared producer.
- **Sharing:** No sharing or cloning is introduced. Each connection keeps its own accepted reference, but the referenced objects may still be shared and mutable.

## Worked trace

Illustrative times are virtual milliseconds from connection to a cold source. Use `key = 'status'` with default comparison. These records are separate objects and remain unmodified during the trace.

| Time | Event | Output and remembered object |
| --- | --- | --- |
| 10 | R1 `{status: 'idle', revision: 1}` | Emit R1; retain R1. |
| 20 | R2 `{status: 'idle', revision: 2}` | Suppress; retain R1. |
| 30 | R3 `{status: 'busy', revision: 3}` | Emit R3; retain R3. |
| 40 | R4 `{status: 'idle', revision: 4}` | Emit R4; retain R4. |
| 50 | Source complete | Complete without another object. |

## Verification obligations

- Check the trace, retained references, and original-object delivery.
- Check custom comparison receives property values and true means suppression.
- Check repeated NaN properties, comparator failures, and property getters that throw.
- Check mutation of a retained object against a selector that stores a primitive key.

## Common confusion

This is not always interchangeable with `distinctUntilChanged(undefined, x => x.status)`. After an object with status `'idle'` is emitted, mutate that same object to `'busy'` and emit it again. `distinctUntilKeyChanged('status')` now reads `'busy'` from both references and suppresses it. The primitive-key selector retained `'idle'`, so it detects the change. Prefer immutable records when the comparison should reflect values at their earlier emission times. Default `===` also means NaN properties compare unequal and object-valued properties compare by identity.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/distinctUntilKeyChanged)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinctUntilKeyChanged.ts)
- [RxJS 7.8.2 distinctUntilChanged implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/distinctUntilChanged.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
