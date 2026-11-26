# Playbook: Porting or Cloning a Reference Codebase (`reference-port`)

Use when the learner wants to deeply understand an existing codebase—and/or contrast a familiar source language with a target language—by building a working port or reimplementation step by step (for example, porting TypeScript `pi-ai` into Rust to master Rust async streams and TS vs. Rust design idioms).

## Decomposition Strategy: Vertical Slices, Not Horizontal Layers

**Avoid** horizontal porting ("Stage 1: port all 40 type definitions; Stage 2: port all utils; Stage 3: port all providers"). Horizontal porting delays runnable feedback for hours and turns learning into tedious transcription.

Instead, carve the reference codebase into **runnable vertical slices**:

1. **Recon the Reference Architecture First**: Map the reference repo's core data flow, public contract, provider/adapter boundaries, and existing tests or fixtures. Identify what can be deferred (e.g., extra providers, auth flavors, optional metrics) vs. what defines the core architecture.
2. **Stage 1 — Minimal End-to-End Vertical Slice**: One minimal request → one real/mocked transport → one observable core output (e.g., streaming text deltas from a single mocked provider). Immediately runnable and testable.
3. **Incremental Behavioral Slices**: Each subsequent stage ports one cohesive behavioral capability from the reference codebase, such as:
   - Incremental framing & stateful event parsing (e.g., SSE chunks → typed stream events)
   - Structured payloads / partial accumulation (e.g., streaming JSON tool-call deltas)
   - Error propagation, cancellation, and usage/lifecycle finalization
   - Polymorphic abstraction across multiple providers / backends (idiomatic to target language!)
   - Cross-cutting concerns (context serialization, retries, real live-endpoint smoke test)
4. **Shared Contract / Parity Fixtures**: Extract real fixtures or trace transcripts from the reference codebase (or its tests) as deterministic test vectors so the port proves behavioral parity without requiring live API keys on every run.

## Mandatory Stage Element: Source vs. Target Language Lens

For cross-language ports, every stage card's *Core Concept* section includes a concise **Source vs. Target Lens**:
- Cite the exact reference file(s) and line range in the source codebase so the learner can inspect the original design directly.
- Highlight **1–2 genuine design shifts** between source and target languages—don't translate mechanically! Examples (TypeScript → Rust):
  - `AsyncIterable` / `async function*` (GC heap frames) ↔ `futures::Stream` / `Pin<&mut Self>` / `async-stream` or manual state machine
  - Structural union types (`{ type: "text_delta", delta: string } | ...`) ↔ Closed algebraic `enum` + exhaustive `match` + `serde(tag = "type")`
  - `AbortSignal` + `try/finally` ↔ `Drop` semantics + `tokio::select!` / cancellation safety
  - Shared mutable closures / objects ↔ Explicit ownership, borrowing, and `Send + 'static` boundaries
  - Class inheritance / dynamic provider registry ↔ Traits, enum dispatch, or trait objects (`Box<dyn Provider>`)

## Common Bypasses to Guard Against

- **Mechanical line-by-line transcription**: Writing "TypeScript in Rust syntax" (e.g., `Rc<RefCell<...>>` or `serde_json::Value` everywhere instead of idiomatic enums/ownership) misses the whole point of a cross-language port.
- **Buffering or flattening async flows**: Collecting the entire upstream response before emitting events instead of preserving true incremental streaming parity.
- **Skipping edge cases present in the reference**: Passing only a clean happy-path fixture while ignoring partial UTF-8/JSON boundaries, stream termination events, or mid-stream error frames that the reference explicitly handles.

## Composing With Other Playbooks

- **+ `language-feature` (Most Common Pairing)**: Use `reference-port.md` to organize *what* feature slice each stage delivers, and `language-feature.md` to design the compiler/runtime proofs for the unfamiliar target-language feature introduced in that stage.
- **+ `core-concept`**: When a slice hinges on a non-trivial protocol or state machine (e.g., incremental JSON tool-call delta assembly), add controlled-stepping and fault-injection tests from `core-concept.md`.
- **+ `library`**: When the port must adopt a new ecosystem crate (e.g., `reqwest`, `bytes`, `tokio`, `pin-project-lite`), let `library.md` guide lifecycle and error-handling verification.
