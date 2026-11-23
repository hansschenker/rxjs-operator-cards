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
Action = () → ()
Close  = () → ()

Schedule ≈ Action → Close
```

Expanding both names yields the talk's shape:

```text
(() → ()) → (() → ())
```

Timing behavior requires a clock and delay:

```text
Scheduler = struct {
  Time() now;
  Close(Action, Duration) schedule;
}
```

This is a teaching abstraction. Actual RxJS scheduling supports state, rescheduling, and `Subscription` objects. A scheduler supplies an execution mechanism and clock; an operator applies a rule using them. A returned closing action can cancel pending work or dispose resources, but cannot undo history.

## 7. What the type does not settle

`T* → T*` is compatible with many operators. It does not specify which values survive, when they are delivered, whether timers or inner connections exist, how completion is handled, or whether upstream execution is shared.

Those choices belong in the card's event/action and lifecycle sections. Optional future refinements may encode more of them formally, but this edition makes no claim to be a complete behavioral type system.

See the [operator index](operator-index.md), [template](../templates/operator-card.md), and [references](references.md).
