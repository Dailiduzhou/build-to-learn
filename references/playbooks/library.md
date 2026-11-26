# Playbook: Learning a Package, Library, or Framework (`library`)

Use when the primary goal is mastering a third-party library, SDK, or framework (e.g., `tokio`, `reqwest`, `axum`, `serde`, `SQLAlchemy`, `React Query`).

## Decomposition Arc (Observable Milestones)

Progress from minimum real invocation to lifecycle and failure mastery—never start with a giant configuration soup:

1. **Minimal Vertical Call**: Smallest runnable program invoking the library's core primitive with deterministic/local input (e.g., local mock server or in-memory store). Observable output proves the wiring works.
2. **Core Abstraction & Composition**: Combine two key primitives of the library (middleware, extractors, custom deserializer, request builders, streaming bodies) to produce a richer observable behavior.
3. **Lifecycle, Resources & Configuration**: Connection pooling, shared state, graceful shutdown, cancellation, timeouts, or explicit resource cleanup.
4. **Failure Modes & Boundaries**: Inspect real library error types, distinguish retryable vs fatal failures, handle partial data, rate limits, or backpressure.
5. **Realistic Extension / Integration**: Adapt the library to a non-trivial custom requirement (custom trait impl, plugin hook, or multi-step workflow) without copying boilerplate blindly.

## Evidence-First Discrimination Scenarios

For each stage, pick a test that only passes if the learner truly understands the library's semantics:
- **Defaults & Config**: Assert behavior that contradicts the naive default (e.g., a timeout fires before a hanging mock server responds).
- **Lifecycle / Cancellation**: Drop or cancel an in-flight handle and assert the mock server / tracker observes cleanup rather than leaking tasks or connections.
- **Error Taxonomy**: Feed distinct failure shapes (4xx auth failure vs 429 rate limit vs malformed body vs connection reset) and assert distinct typed outcomes.

## Common Bypasses to Guard Against

- Calling blocking APIs inside an async runtime (e.g., `std::thread::sleep` or blocking HTTP inside `tokio`).
- Catching/erasing all errors into opaque strings (`anyhow`/generic `catch`) when the stage targets typed error discrimination.
- Recreating expensive clients/pools per call instead of sharing them across requests.
- Copy-pasting high-level convenience wrappers when the stage targets lower-level streaming or middleware hooks.

## Composing With Other Playbooks

- **+ `language-feature`**: When using the library surfaces tricky language rules (e.g., `axum` handler trait bounds, `tokio::select!` cancellation safety, Rust `Stream` pinning), zoom in with a compiler-proven or runtime-proven stage from `language-feature.md`.
- **+ `reference-port`**: When a reference codebase uses library A (e.g., TS `fetch`/OpenAI SDK) and the port uses library B (e.g., Rust `reqwest` + `eventsource`/custom stream), highlight how library idioms differ rather than mimicking library A's API 1:1.
