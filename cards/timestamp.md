# timestamp

```text
Operator:       timestamp
Stream shape:   T* → Timed<T>*
State:          Unit — no previous value or time
Initial state:  ()
Value step:     (Unit × T × Time) → (Unit × Timed<T>)
Ownership:      One clock read per source value in each operator subscription
Lifecycle:      Forward source terminals; clock-read errors terminate; cancellation closes upstream silently
```

**Family:** Time measurement · **Reference:** RxJS 7.8.2 · **Scope:** Default or supplied TimestampProvider · **Format:** Compact

> Attach the current clock reading to each arriving value without changing when it is delivered.

## Notation and scope

- `Timed<T>` is `{ value: T, timestamp: Time }`. The explicit `Time` input is the provider's `now()` reading for that arrival.

- The default provider uses the date clock. A custom provider determines its own units and epoch; this operator reads time but does not schedule delivery.

- No previous timestamp or payload history is retained for the behavior, and no sharing is introduced. The wrapped payload remains the same value/reference.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/timestamp.ts)
- [Related map implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/map.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
