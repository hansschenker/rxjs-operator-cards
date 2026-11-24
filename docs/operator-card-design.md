# The operator-card idea

An operator card gives one reusable answer to the question: **what happens to values and connections when this operator is used?**

The format emerged from Hans Schenker and ChatGPT's discussion of Erik Meijer's talk on duality. Compact signatures reveal useful common structure. Operators sharing a signature can still differ in timing, admission, cancellation, and termination. A card keeps both views together.

```text
Operator description = Type shape + Behavior rules
```

## Intuition → Specification → Verification

### Intuition

Start with what flows and what the next event changes. For example, `switchMap` keeps the latest accepted inner connection; `buffer` publishes collected arrays; `scan` publishes remembered state after each input.

The domain-specific function belongs to the application. The RxJS operator supplies the reusable coordination pattern. A projection need not be arithmetic, and a predicate need not concern any particular business object.

### Specification

Give a function-first type shape and state the overload/policy being described. Explain the local rule, whole-stream cardinality, retained state, and event/action rules. A type such as `(T! → R*) → T* → R*` is shared by the four flattening policies, so the signature alone cannot choose a policy.

Treat normal completion, errors, and consumer closure as different events. Source completion may be delayed at the output while accepted inner work finishes. A consumer can instead close the entire owned execution without receiving a terminal notification.

### Verification

Provide an observable trace and a few obligations that distinguish this operator from its neighbors. A trace is an example, not a proof of all executions. State scheduling and connection assumptions so that the example is reproducible.

Obligations are potential checks. The separate [validation report](validation.md) states which checks were actually performed for this edition.

## Reading a card through the policy axes

| Axis | Question answered by the card |
| --- | --- |
| Source | Which source, notifier, and inner connections participate? |
| Trigger | Which event causes an action? |
| Value | Is the payload transformed, selected, paired, grouped, or forwarded? |
| Cardinality | What can one event produce, and what can the whole connection emit? |
| Time | Which clock, delay, or deadline determines eligibility? |
| Concurrency | Which inner executions may be active, queued, or ignored? |
| Cancellation | Who owns each connection, and what closes it? |
| Termination | What happens on source/inner completion and failure? |

**Sharing** is also stated explicitly as an execution-ownership property: separate downstream connections may have independent state, observe a live source, or participate in a shared replay boundary. It is not implied by the `*` suffix.

## A small transition model

A stateful rule can be written as:

```text
step :: State × Event → State × Action[]
```

`Event` includes the source, inner, notifier, timer, and closure events relevant to that operator. `Action[]` is a finite ordered list of decisions, such as emitting a value, forwarding a terminal notification, opening/closing a connection, or scheduling/canceling work.

This connects the readable card to a Mealy-style specification. A local transition rule describes a step. Applying it over an event history produces an execution trace. Later work can derive state-transition tables, diagrams, BDD scenarios, and checks from reviewed cards.

This repository's first edition does not implement that generator or claim formal equivalence proofs. It establishes the common vocabulary and reference examples needed to do that work reliably.

## Design decisions

1. **Preserve nesting.** `(T*)*` remains a stream of streams until an explicit policy flattens it.
2. **Name cardinality scope.** Filtering forwards zero or one value per input but may emit many over a whole connection.
3. **Keep lifecycle visible.** A value type does not promise completion or producer cancellation.
4. **Use ordinary language beside symbols.** A reader should be able to predict a simple trace before reading implementation code.
5. **Pin the reference.** This edition describes RxJS 7.8.2 and identifies specialized overloads.
6. **Keep the repository usable as documents.** Markdown cards need no application, build step, or dependency installation.

## Standard card sections

Every card opens with the same seven-line compact notation block: **Operator**, **Stream shape**, **State**, **Initial state**, **Value step**, **Ownership**, and **Lifecycle**. This gives readers a consistent overview of the dataflow, local memory, value processing, and lifetime before they read the explanation. The [notation guide](notation.md#8-compact-operator-notation) defines its symbols and scope.

See the [template](../templates/operator-card.md). State only what applies, but do not omit a lifecycle or ownership rule merely because the type shape is simple.

The first examples are indexed in [operator-index.md](operator-index.md). Coverage beyond those cards is tracked in the [roadmap](roadmap.md).
