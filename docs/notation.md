# Cω-inspired notation for RxJS behavior

The purpose of this notation is to make an operator's value structure readable at a glance, then attach the rules that determine its execution. It is explanatory notation, not TypeScript syntax or a static type checker.

## 1. Vocabulary

| Form | Reading |
| --- | --- |
| `T`, or `T!` in a local value position | One ordinary value of type T |
| `T?` | Zero or one T, within the stated scope |
| `T+` | One or more T values, with any required progress/success condition stated |
| `T*` | Zero or more T values over time; in stream signatures, `Observable<T>` |
| `T[]` | One array payload containing T elements |
| `R(T)` | A function accepting T and returning R; also `T → R` |
| `R(A, B)` | A function accepting both inputs; also `(A × B) → R` |
| `T()` | A function with no arguments returning T; also `() → T` |
| `A \| B` | One value belonging to either alternative |
| `A & B` | One value satisfying both type requirements |
| `A × B` | A pair containing an A and a B |
| `struct { T member; U other; }` | A record with named fields |
| `()` | No arguments, or no useful result, according to its function position |

A function may be implemented by a closure. Its signature does not describe the captured environment.

Arrow signatures are function-first and data-last when appropriate. For example:

```text
map :: (T! → R!) → (T* → R*)
take :: Count → (T* → T*)
debounceTime :: Duration → Scheduler → (T* → T*)
```

These arrows describe dependencies; they do not claim that RxJS uses that exact curried JavaScript API. Signatures may specialize to Observable inputs and omit optional indices/overloads. Each card names its scope.

## 2. Local cardinality and whole-stream cardinality

Always state the scope. In a supplied pure function's signature, `T! → R!` means one ordinary input produces one ordinary result, assuming normal return. In a stream position, a cardinality describes the number of `next` notifications delivered through one connection.

| Operator | One processed source value | Whole stream |
| --- | --- | --- |
| `map(f)` | `T! → R!` | `T* → R*` |
| `filter(p)` | `T! → T?` | `T* → T*` |
| `take(1)` | Admit only the first value | `T* → T?` |
| Seeded `scan(step, seed)` | One updated state after each input | `T* → S*` |
| Seeded `reduce(step, seed)` | Update state without emitting it yet | `T* → S?` |

`T?` in an output stream position still denotes an Observable. It does not mean that the operator returns a scalar `T | undefined`. Likewise, `T?` differs from `(T | Undefined)*`, which permits many emitted values, including actual `undefined` payloads.

Upper bounds are not progress guarantees. A stream described by `T?` can remain silent forever. Whole-stream `T!` and `T+` need explicit conditions: seeded `reduce` emits exactly one value **on normal source completion while its output remains connected**, including the seed for an empty source. Early error or closure prevents that final result.

Cardinality counts `next` notifications. It does not count `complete`, `error`, or connection closure as payloads. An emitted empty array is still one value.

## 3. Arrays and nested streams

| Expression | Meaning |
| --- | --- |
| `T[]` | One array |
| `T*` | A stream of T values |
| `(T[])*` | A stream whose values are arrays |
| `(T*)*` | A stream whose values are Observable descriptions |
| `(T*)[]` | One array containing Observable descriptions |
| `A* × B*` | Two input streams |
| `(A × B)*` | One output stream of pairs |

The nested shape is preserved:

```text
buffer :: U* → (T* → (T[])*)
window :: U* → (T* → (T*)*)

mergeAll, concatAll, switchAll, exhaustAll :: (T*)* → T*
```

The four flattening operators have the same broad shape and different coordination rules. Historical Cω aligned streams with iterators and automatically flattened nested streams; this RxJS dialect deliberately keeps `(T*)*` distinct from `T*`. See the [Cω paper](references.md).

Mapping and flattening explain a type composition. An equation such as `switchMap(f) = map(f) then switchAll` is not an unrestricted statement about execution: when `f` runs, which inputs are admitted, and effects/errors inside `f` can change observable behavior.

## 4. Union, product, and intersection

```text
merge         :: (A* × B*) → (A | B)*
zip           :: (A* × B*) → (A × B)*
combineLatest :: (A* × B*) → (A × B)*
```

`merge` forwards either kind of payload. `zip` and `combineLatest` each produce pairs, but apply different triggers and pairing rules. Their output is not the type intersection `A & B`.

An intersection is appropriate for a grouped Observable:

```text
Group<K, T> = T* & struct { K key; }
groupBy :: (T! → K!) → T* → Group<K, T>*
```

One group is both an Observable and an object with a key. This corresponds to RxJS's [GroupedObservable](https://rxjs.dev/api/index/interface/GroupedObservable).

A record can describe an enriched payload:

```text
Timed<T> = struct { T value; Time timestamp; }
timestamp :: Scheduler → T* → Timed<T>*
```

A tagged union can represent ordinary application states:

```text
SearchState = Loading | Results(Result[]) | Failed(Problem)
```

Here `Failed(Problem)` can be delivered by `next`. It is distinct from terminating the connection through the error channel.

## 5. Pull and Push

The value-only sketches are:

```text
Pull<T> ≈ () → (() → T)
Push<T> ≈ (T → ()) → ()
```

Pull opens an iterator and lets the consumer request observations. Push receives a consumer interface and delivers observations to it. Values move from producer to consumer in both cases; initiative and argument/result roles differ.

These sketches omit state, effects, terminal outcomes, and cleanup. A fuller normalized model is:

```text
Close = () → ()
Outcome<T> = Next(T!) | Complete | Failed(Err!)

Iterator<T> = struct {
  Outcome<T>() next;
  Close close;
}

Pull<T> ≈ () → Iterator<T>

Observer<T> = struct {
  (T! → ()) next;
  (Err! → ()) error;
  (() → ()) complete;
}

Push<T> ≈ Observer<T> → Close
```

The fuller Pull form normalizes failure into an explicit alternative; a real iterator may throw. The Push form abstracts the `Subscription` returned by RxJS to its closing capability. `Err` names the error-channel payload, not an additional type parameter enforced by RxJS.

The protocol is separate from the payload type:

- Zero or more `next` notifications may be followed by one `complete` or `error`.
- A stream can also continue indefinitely or remain silent without terminating.
- No further notifications are delivered through a closed or terminated subscriber.
- Consumer closure is a lifecycle action and does not itself notify `complete`.

Neither simplified type determines synchronous/asynchronous delivery, replay, or sharing. In the examples, creating a pipeline describes work; subscription establishes the connection. A live external producer can exist before that connection.

## 6. Scheduler and closing action

```text
ScheduledAction = () → ()
Close           = () → ()

Schedule ≈ ScheduledAction → Close
```

Expanding both names yields the talk's shape:

```text
(() → ()) → (() → ())
```

Timing behavior requires a clock and delay:

```text
Scheduler = struct {
  Time() now;
  Close(ScheduledAction, Duration) schedule;
}
```

This is a teaching abstraction. Actual RxJS scheduling supports state, rescheduling, and `Subscription` objects. A scheduler supplies an execution mechanism and clock; an operator applies a rule using them. A returned closing action can cancel pending work or dispose resources, but cannot undo history.

## 7. What the type does not settle

`T* → T*` is compatible with many operators. It does not specify which values survive, when they are delivered, whether timers or inner connections exist, how completion is handled, or whether upstream execution is shared.

Those choices belong in the card's event/action and lifecycle sections. Optional future refinements may encode more of them formally, but this edition makes no claim to be a complete behavioral type system.

See the [operator index](operator-index.md), [template](../templates/operator-card.md), and [references](references.md).

## 8. Compact operator notation

Each operator card begins with a fenced `text` block containing the same seven fields. Read Stream shape as the external dataflow, State and Initial state as its memory, Value step as the local source-value transition, and Ownership and Lifecycle as its execution context.

For example, a `pairwise` value model reads:

```text
Operator:       pairwise
Stream shape:   T* → (T × T)*
State:          Option<T>
Initial state:  None
Value step:     (Option<T> × T) → (Option<T> × Option<T × T>)
Ownership:      One state per operator subscription
Lifecycle:      Forward completion/error; cancellation closes silently
```

### Local values, memory, and actions

| Form | Meaning in the compact block |
| --- | --- |
| `Option<T>` | Local presence: `None` or `Some(value)`; the stored value may itself be `null` or `undefined` |
| `Unit` | A single state with no remembered data; its value is written `()` |
| `Nat` | A nonnegative integer, such as a zero-based index or a count |
| `Bool` | `false` or `true` |
| `S` | The operator-specific state type described in the State row |
| `T[]` | A finite array of values, in memory or as one emitted payload |
| `Action[]` | A finite ordered list of actions selected by the step; `[]` means no action |
| `Time` | A reading from the specified scheduler's clock |
| `TimerId`, `InnerId` | Symbolic identities of owned scheduled work or inner subscriptions |

`Option<T>` is a local value container, distinct from the stream-cardinality notation `T?`. In an output position of a value step, `None` means no downstream value for that step; it never means emitting `undefined`. Likewise, `Action[]` is an ordinary finite list, never an Observable of actions.

Actions can include delivering a value or terminal notification, opening or closing a subscription, or scheduling or canceling work. Their order is significant. Per-card state aliases are explained in **Remembers**. Operator arguments such as the projection, count, or duration are fixed parameters of the card's scoped rule rather than newly supplied inputs at each step.

### Scope of the summary

The State row describes the memory relevant to an active execution, plus completion flags that are needed while other work remains. Overall closure is governed by Lifecycle and the full event/action table. A compact header is a readable summary, not a complete six-tuple or a claim to model every implementation detail.

Value step normally describes a source `next` while the relevant connection is open, assuming a supplied function returns normally. A timed step can include an explicit `Time` input read from the scheduler. If the result is an ordinary value or `Option` value, it is the local downstream-value result. If the result is `Action[]`, the card also needs explicit coordination or resource decisions. Setup, timer, notifier, inner, completion, error, and cancellation events remain explicit in the card's event/action table. For higher-order operators, distinguish outer-value processing from inner-value forwarding.

Initial state is read together with the Connect row: setup may emit prefixes or an initial window, open a notifier before the source, or complete without ever subscribing upstream. State ownership must identify sharing boundaries; observing a shared source does not automatically share downstream operator state.

The compact block is interpreted under the card's **Scope** declaration immediately below it. Error and cancellation can interrupt processing. These explanatory signatures do not enforce pure user functions, clone mutable seeds, or establish equivalence for every scheduler and reentrant execution.
