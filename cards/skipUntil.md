# skipUntil

```text
Operator:       skipUntil
Stream shape:   T* → T*
State:          Bool — forwarding gate
Initial state:  false
Value step:     (Bool × T) → (Bool × Option<T>) — forward only after the gate opens
Ownership:      One gate and source/notifier subscription pair per operator subscription
Lifecycle:      Notifier next opens gate; source complete completes; either error forwards; cancel closes silently
```

**Family:** Selection · **Reference:** RxJS 7.8.2 · **Scope:** skipUntil(notifier), with an Observable notifier · **Format:** Compact

> Ignore source values until the notifier sends a value, then forward subsequent source values.

## Notation and scope

The notifier is subscribed before the source. Its first next opens the gate and closes the notifier subscription; that next is not an output value.

Notifier completion alone leaves the gate unchanged. If it completes without a value, the result stays closed to source values until the source itself terminates. Source completion ends the result without waiting for the notifier.

No scheduling or sharing is introduced; the source and notifier determine event timing.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/skipUntil.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
