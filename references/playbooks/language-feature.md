# Playbook: Learning a Language Feature (`language-feature`)

Use when the primary goal is mastering a language's type system, memory/ownership model, concurrency/async semantics, traits/generics, macros, or runtime execution model (e.g., Rust `Stream` + `Pin` + lifetimes, TypeScript conditional types, Go channels & context cancellation).

## Decomposition Arc (Observable Milestones)

Embed every language feature inside a real, evolving mini-project—not disconnected textbook puzzles:

1. **Concrete Need First**: Build the simplest runnable slice where the naive approach *almost* works, exposing why the language feature exists in the first place.
2. **Happy-Path Idiomatic Use**: Introduce the core construct (e.g., implementing `Iterator` or `Stream` manually via `poll_next`, borrowing slices across frames, generic trait bounds) with observable runtime behavior.
3. **Boundary Rules & Compiler Contracts**: Introduce constraints that force engagement with the rule's sharp edges (e.g., `Pin<&mut Self>` projection, zero-copy lifetimes tied to an input buffer, `Send + Sync` across spawned tasks, variance).
4. **Composition & Ergonomics**: Compose the feature with errors, state machines, combinators, or higher-level abstractions cleanly without fighting the compiler.
5. **Misconception Stress Test**: Trigger a real-world failure mode (cancellation safety, drop order, aliasing, borrow-across-await) and resolve it cleanly.

## Evidence-First Discrimination Scenarios

Design tests that verify the **language rule itself**, not merely output equality:
- **Compile-time / Signature Contracts**: Acceptance tests call the learner's API in ways that enforce the type invariant—for example, spawning the returned `Stream` onto a multi-threaded executor (`assert_send`), passing a non-`'static` borrowed buffer, or using `compile_fail` / type-assertion tests where appropriate.
- **Runtime Behavioral Proofs**: Prove lazy/incremental execution (items produced only when polled, not eagerly computed), deterministic `Drop` ordering, or zero extra allocations/copies when relevant.

## Common Bypasses to Guard Against

- **Ownership/Lifetime dodging**: Sprinkling `.clone()`, `.to_owned()`, `Arc<Mutex<...>>`, or `Box::leak` everywhere when borrowing or structured state transitions were the learning target.
- **Type-system escape hatches**: Using `unsafe`, `as any`, `@ts-ignore`, `Box<dyn Any>`, or stringly-typed dicts to avoid modeling invariants in the type system.
- **Eager evaluation disguising as lazy/streaming**: Collecting into a `Vec` and calling `iter()` / `stream::iter()` when incremental `poll_next` / lazy evaluation is the target concept.

## Composing With Other Playbooks

- **As secondary lens on `reference-port`**: Map each source-language pattern to its target-language counterpart explicitly inside the stage card's *Source vs. Target Lens* (e.g., TS `async function*` heap-allocated generator frames vs Rust stackless `enum` state machines & `Pin<&mut Self>`; TS discriminated union objects vs Rust algebraic `enum` + exhaustive `match`).
- **As secondary lens on `library` or `core-concept`**: When a library error or systems requirement hits a deep language mechanic, dedicate one focused stage (or micro-stage) to proving that language rule cleanly before moving on.
