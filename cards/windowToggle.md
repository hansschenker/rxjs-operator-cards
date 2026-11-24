# windowToggle

```text
Operator:       windowToggle
Stream shape:   T* → (T*)*
State:          WindowSlot<T>[] — live windows and their closing subscriptions
Initial state:  []
Value step:     (S × T) → (S × Action[]) — deliver to every currently open window
Ownership:      One window set per outer subscription; each live window multicasts to its observers
Lifecycle:      Source completion finishes all; source/closing errors fail all; opening errors fail outer; cancel closes owned work
```

**Family:** Grouping · **Reference:** RxJS 7.8.2 · **Scope:** windowToggle(openings, closingSelector), with Observable inputs · **Format:** Compact

> Publish a live window for each opening value and complete it on its closing notifier’s first value.

## Notation and scope

WindowSlot<T> contains a live Subject-backed window and its closing subscription identity. Openings subscribe before the source; there is no initial window until an opening next arrives.

Closing next completes and removes its window. Closing completion alone is ignored; openings completion only prevents future openings. Source values go to all open windows without replay or buffering.

Source errors, closing errors, and closing-selector failures error the windows and outer result. An openings error is forwarded to the outer result without an error notification to held inner observers.

Outer cancellation closes owned source/notifier subscriptions. The source subscriber’s teardown disposes remaining window Subjects without sending them complete; this teardown is installed after the openings subscription is established. No scheduler is introduced.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/windowToggle.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
