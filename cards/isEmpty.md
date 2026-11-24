# isEmpty

```text
Operator:       isEmpty
Stream shape:   T* → Bool?
State:          Unit — no value history while open
Initial state:  ()
Value step:     (Unit × T) → (Unit × Action[]) — emit false and complete
Ownership:      One source connection per operator subscription
Lifecycle:      First value answers false; empty completion answers true; errors forward; cancellation closes silently
```

**Family:** Emptiness decision · **Reference:** RxJS 7.8.2 · **Scope:** isEmpty() · **Format:** Compact

> Answer false on the first value, or true when the source completes without producing any value.

## Notation and scope

- No payload needs to be retained: the first arrival ends the decision immediately and closes upstream.

- Completion while the decision is still open emits `true` and then completes. Error produces no boolean answer, and an indefinitely silent source produces none.

- Source timing determines when the decision becomes possible. No scheduler or sharing is introduced; consumer cancellation sends no answer or completion.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/isEmpty.ts)
- [Notation](../docs/notation.md#8-compact-operator-notation)
- [Operator index](../docs/operator-index.md)
