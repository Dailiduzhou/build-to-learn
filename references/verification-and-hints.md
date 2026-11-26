# Scaffolding, Verification, Anti-Bypass & Hint Protocol

Read this file when preparing stage scaffolds, checking learner implementations, coaching a stuck learner, or running transfer challenges.

---

## 1. Fading Scaffolding Matrix

Scaffolding reduces friction early so the learner focuses on the target concept rather than boilerplate, then fades deliberately so the learner practices real architecture and testing as mastery grows.

| Tier | Typical Stages | Agent Prepares (Allowed) | Learner Owns (Agent Never Writes) |
| :--- | :--- | :--- | :--- |
| **`early`** | Stages 1–2, or first contact with an unfamiliar runtime/toolchain | Project manifest & dependencies, mocks/fixtures, failing acceptance tests, minimal file/trait/function signatures with `todo!()` / `NotImplementedError` stubs | Core logic inside the stubbed functions/types |
| **`mid`** | Middle stages (roughly 3–5) | Mocks/fixtures, black-box integration/contract tests against public entrypoints | Internal module structure, helper types/enums, state transitions, and core implementation |
| **`late`** | Final / capstone / complex integration stages | High-level contract spec, shared test vectors, or external fault harness | Public & internal API design, error taxonomy, core implementation, and targeted unit tests |

**Sanity-check every scaffold before handing it over:** run the project's build/test command yourself to confirm the harness compiles and fails *strictly* because the learner's target behavior is unimplemented—not because of broken imports, missing crate features, or syntax errors in the scaffold. Silence harmless unused-field/parameter warnings on intentional early stubs (e.g., `#[allow(dead_code)]` in Rust) so the learner's test output stays clean and focused on the real failure.

---

## 2. Strict No-Spoiler Boundary

**Never edit, patch, or auto-complete the learner's target implementation files**, even when:
- the compiler throws a long, confusing error;
- tests are failing by a tiny off-by-one bug;
- the learner says "help me fix this" or "I'm stuck."

**Allowed edits during a stage:**
- Fixing genuine bugs in *Agent-authored* test harnesses, mocks, or build configuration (explain clearly that the bug was in the harness, not their code).
- Adding a new test case to expose a discovered bypass or activate a Transfer Challenge.

**Explicit Opt-In Escape Hatch:**
Only reveal a working reference implementation if the learner **explicitly asks for the answer** (e.g., "直接给我参考实现", "show me the full solution", "just write this part for me"). Even then:
1. Show the explanation alongside the snippet and confirm whether they want to type/adapt it themselves or have you apply it.
2. Record the exact sub-part under `Assisted Points` and `Deferred Re-checks` in `.learn/PROGRESS.md` so a transfer challenge or later stage checks independent recall.

---

## 3. Four-Level Hint Ladder

When build/tests fail or the learner is stuck, start low and escalate **at most one level per turn** unless the learner explicitly requests a stronger hint (e.g., "give me a more concrete hint"). Explain clearly so each hint builds transferable debugging instincts.

| Level | Name | What to Provide | What NOT to Provide |
| :--- | :--- | :--- | :--- |
| **L1** | **Observed Phenomenon** | Distill the exact compiler/runtime failure and contrast *actual* vs *expected* behavior in plain terms. | Do not diagnose the exact line fix or show replacement code. |
| **L2** | **Pointer & Clue** | Point to the exact file/line/type signature causing the mismatch, plus the relevant official doc concept or reference source line. | Do not explain the full solution structure or write code. |
| **L3** | **Mechanism & Rule** | Explain the underlying language rule, ownership/lifetime constraint, protocol state transition, or source-vs-target mental model shift (e.g., why Rust requires `Pin` for self-referential futures whereas JS/TS heap-allocates generator frames). | Do not write drop-in code for their specific file. |
| **L4** | **Local Pseudocode or Minimal Counterexample** | Sketch a 3–6 line structural pseudocode outline OR a tiny standalone toy snippet illustrating the pattern on unrelated data. | Still do not edit their file or dump the complete drop-in patch. |

---

## 4. Anti-Bypass Audit (Checking `git diff`)

Passing tests is necessary, **never sufficient**. After acceptance tests go green, inspect `git diff` against the stage's diff anchor and verify these questions:

1. **Concept Exercised**: Did the implementation actually use the stage's primary mechanism, or did it route around it?
   - *Streaming / Async*: Did they buffer the entire payload (`.collect()`, `read_to_string()`, `await all`) before emitting events?
   - *Language Feature*: Did they dodge ownership/lifetimes/generics via blanket `.clone()`, `Box::leak`, `unsafe`, `as any`, or dynamic untyped maps when typed/borrowed handling was the point?
   - *Library / Lifecycle*: Did they open resources without cleanup/cancellation, swallow errors with `.unwrap()` / empty `catch`, or ignore backpressure/timeouts?
   - *Reference Port*: Did they hardcode fixture outputs or copy source-language anti-patterns without engaging with the target language's core abstraction?
2. **Genuine Behavior vs Hardcoding**: Would the code still work if chunk boundaries, payload order, or error timing changed?
3. **If a Bypass Is Found**:
   - Do **not** mark `Functional: pass`.
   - Do **not** fix their code for them.
   - Praise what works, then concretely explain the bypass ("Right now `parse_stream` reads the full body with `text().await` before splitting lines, so the first token cannot arrive until the HTTP response closes").
   - Either add/strengthen a targeted test that fails under the bypass (e.g., a mock stream that sends chunk 1, waits for the consumer to observe event 1, and *only then* sends chunk 2) OR state the structural refactor required, then let the learner iterate.

---

## 5. Transfer Challenge Protocol

A Transfer Challenge tests whether the learner can **apply the same concept under a changed condition** rather than having memorized one happy path.

### Good Transfer Patterns (Pick One, ~10–15 min)
1. **Boundary Perturbation**: Split an SSE frame or multi-byte UTF-8 character right across two network chunks; pass empty/malformed/out-of-order inputs.
2. **Injected Fault or Cancellation**: Drop the consumer mid-stream (verify cleanup/cancellation), inject a mid-stream HTTP/network error, or trigger a timeout.
3. **Constraint Shift**: Change a trait bound (e.g., require `Send + 'static`), swap an input source (in-memory reader → async socket), or add a new event variant.
4. **Targeted Mechanism Check** *(optional companion, 1–2 sentences)*: Ask one sharp counterfactual ("If the caller stops polling after the second event, what happens to the underlying HTTP connection in your implementation vs TypeScript's `AsyncGenerator`?").

### Recording Functional vs Transfer Separately
- Never block the learner indefinitely if they want to keep momentum.
- If they complete and pass the variation:
  - Record `Functional: pass` and `Transfer: pass` with concrete evidence in `.learn/PROGRESS.md`.
- If they explicitly ask to skip the transfer challenge (e.g., "skip transfer for now, move to stage 2"):
  - Record `Functional: pass` and `Transfer: skipped` separately.
  - Append a concrete item to `Deferred Re-checks` in `.learn/PROGRESS.md` describing the unverified variation.
  - When generating a future stage (usually `NN+1` or `NN+2`), weave that variation naturally into the new stage's edge-case test or transfer challenge so the gap still gets closed organically.
