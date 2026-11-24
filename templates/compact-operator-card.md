# OPERATOR_NAME

```text
Operator:       OPERATOR_NAME
Stream shape:   INPUT_STREAM → OUTPUT_STREAM
State:          RUNNING_EXECUTION_MEMORY
Initial state:  INITIAL_MEMORY
Value step:     (STATE × INPUT_VALUE) → (STATE × VALUE_RESULT_OR_ACTIONS)
Ownership:      STATE_AND_CONNECTION_OWNER
Lifecycle:      COMPLETION_ERROR_AND_CANCELLATION_RULES
```

**Family:** FAMILY · **Reference:** RxJS 7.8.2 · **Scope:** SPECIFIC_OVERLOAD · **Format:** Compact

> One sentence explaining what flows and what happens on a new value or relevant trigger.

## Notation and scope

Define the compact State row's components and any local aliases. `S` abbreviates that row; `Option<T>` is a local optional value; `Action[]` is a finite ordered list of decisions. Use the [notation guide](../docs/notation.md#8-compact-operator-notation).

Explain essential source, timer, notifier, inner, and terminal triggers, with time, cancellation, and sharing explicit. State assumptions and excluded overloads. For compatibility forms, identify the deprecated pipeable import and the modern counterpart. Keep runtime checks distinct from source review.

## References

- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/OPERATOR_NAME.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
