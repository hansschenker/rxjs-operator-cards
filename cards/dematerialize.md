# dematerialize

```text
Operator:       dematerialize
Stream shape:   Notification<T>* → T*
State:          Unit — no payload history
Initial state:  ()
Value step:     (Unit × Notification<T>) → (Unit × Action[]) — enact the notification kind
Ownership:      One source subscription per operator subscription
Lifecycle:      Terminal payload ends output; source terminal also forwards; cancellation closes upstream silently
```

**Family:** Notification interpretation · **Reference:** RxJS 7.8.2 · **Scope:** Well-formed Next, Error, or Complete notification payloads · **Format:** Compact

> Turn each notification payload back into a value, error, or completion on the output connection.

## Notation and scope

- `Notification<T>` is `Next(T) | Error(unknown) | Complete`. `Next(x)` produces output `next(x)`; an Error or Complete payload terminates output and closes upstream.

- The source stream can also complete or error directly; these terminal notifications are forwarded if the output remains open. Payloads after an enacted terminal cannot be delivered through that closed subscription.

- Interpretation follows source timing with no scheduler or sharing. A source value containing `undefined` inside a Next notification remains a real output value.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/dematerialize.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
