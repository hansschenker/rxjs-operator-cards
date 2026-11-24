# toArray

```text
Operator:       toArray
Stream shape:   T* → (T[])?
State:          T[] — all values received so far
Initial state:  [] — freshly allocated per subscription
Value step:     (T[] × T) → (T[] × Action[]) — append without emitting
Ownership:      One state per operator subscription; owns its array
Lifecycle:      Completion emits the array then completes; error forwards without array; cancellation discards silently
```

**Family:** Aggregation · **Reference:** RxJS 7.8.2 · **Scope:** toArray() · **Format:** Compact

> Collect every source value into one array and publish it after normal completion.

## Notation and scope

T[] is an array payload, so an empty source emits one empty array before completing. The ? bounds the number of array emissions, not the number of values stored inside the array.

An endless source never produces the final array, and memory can grow with the source. No scheduler or sharing is introduced; error and cancellation do not flush.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/toArray.ts)
- [RxJS 7.8.2 reduce implementation](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/reduce.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
