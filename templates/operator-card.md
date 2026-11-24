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

**Family:** FAMILY · **Reference:** RxJS 7.8.2 · **Scope:** SPECIFIC_OVERLOAD

> One sentence explaining what flows, what changes on a new value, and why this operator is useful.

## Type shape

```text
OPERATOR_NAME :: ARGUMENTS → INPUT → OUTPUT
```

Describe the supplied functions or values. These are explanatory, function-first signatures, not literal TypeScript declarations. Keep nested streams explicit.

## Intuition

Describe a small practical situation. Explain what moves over time before introducing implementation details.

## Local rule and whole stream

| Scope | Rule |
| --- | --- |
| One source event | LOCAL_RULE |
| Whole connection | GLOBAL_SHAPE_AND_CARDINALITY |

State the conditions for any exactly-one or nonempty claim. Cardinality counts `next` values, not terminal notifications.

## Remembers

List the state needed for the behavior. Distinguish per-connection state from state owned by a sharing boundary.

Define any operator-specific state aliases used in the opening block. Its `Value step` summarizes source-value processing; subscription, timer, notifier, inner, and terminal events belong in the event/action table. Use the [compact-notation conventions](../docs/notation.md#8-compact-operator-notation).

## Event → action

| Event | State change and action |
| --- | --- |
| Connect | CONNECTION_RULE |
| Source next | NEXT_RULE |
| Relevant timer, notifier, or inner event | AUXILIARY_RULE_OR_NOT_APPLICABLE |
| Source complete | COMPLETION_RULE |
| Source error | ERROR_RULE |
| Downstream close | CLOSURE_RULE |

Add rows for projection errors, inner errors, and notifier completion where they matter. Omit irrelevant rows rather than inventing events.

## Time, cancellation, and sharing

- **Time:** State where the clock and delivery timing come from. Name scheduler assumptions and avoid boundary ties in examples.
- **Cancellation:** Name the owned connections/resources released. Closing a connection does not send `complete` or undo completed external effects.
- **Sharing:** State whether this operator introduces sharing, inherits a shared source, or emits live inner streams.

## Worked trace

Use a small table with event order, output, and any relevant state. Times must name their reference (source connection, inner connection, or scheduler clock). State whether sources are cold or live, and when they terminate.

## Verification obligations

List a few observable properties or distinguishing scenarios. These are review/test obligations, not a claim that an automated test suite exists.

## Common confusion

Explain the one or two misunderstandings most likely to change behavior. Prefer concrete counterexamples.

## References

- [RxJS documentation](https://rxjs.dev/api/index/function/OPERATOR_NAME)
- [RxJS 7.8.2 source](https://github.com/ReactiveX/rxjs/blob/7.8.2/src/internal/operators/OPERATOR_NAME.ts)
- [Notation](../docs/notation.md)
- [Operator index](../docs/operator-index.md)
