# Playbook: Learning a Core Concept or Mechanism (`core-concept`)

Use when the primary goal is understanding an underlying computer science, systems, protocol, or engineering mechanism from first principles (e.g., SSE/HTTP chunked framing, event loops, backpressure, Raft/consensus, tokenizers, B-trees, parser combinators, agent tool-use loops).

## Decomposition Arc (Observable Milestones)

Follow a first-principles build loop (*Problem → Concept → Build It → Compare with Production → Verify under Stress*):

1. **Minimal Mechanism With Controlled Input**: Build the tiniest end-to-end version using clean, deterministic, hand-crafted input so the learner can literally print/inspect every step of the state machine.
2. **Observable State Transitions**: Expose intermediate state or event traces (e.g., partial buffer state, token transitions, queue depth, retry backoff ticks) so the mechanism is visible, not a black box.
3. **Real-World Messiness & Boundaries**: Feed nasty, realistic inputs (split frames across arbitrary byte boundaries, interleaved events, malformed payloads, empty/EOF edges).
4. **Fault Injection & Invariants**: Deliberately inject failures (dropped connections, slow consumers causing backpressure, duplicate/out-of-order deliveries, timeouts) and verify the system's core invariant still holds.
5. **Bridge to Ecosystem ("Use It / Compare")**: Briefly contrast the learner's from-scratch implementation against how production libraries/runtimes optimize or wrap the same mechanism (connecting first-principles intuition to day-to-day tools).

## Evidence-First Discrimination Scenarios

Design deterministic scenarios where a flawed mental model *must* fail:
- **Controlled Stepping**: Drive the system step-by-step (manual ticks, chunk-by-chunk feeds, scripted fault schedules) instead of relying on flaky `sleep()` timings.
- **Invariant Assertions**: Assert structural laws—not just final values (e.g., "no event is emitted twice across reconnect", "memory buffer stays bounded when consumer pauses", "output frame count matches incremental chunk arrival before EOF").

## Common Bypasses to Guard Against

- **Calling a black-box library for the exact mechanism being learned**: Using an external parser/scheduler library when the stage goal is building that state machine from scratch. (Allowed for orthogonal plumbing like CLI args or test HTTP servers, never for the core concept itself.)
- **Happy-path-only assumptions**: Assuming one network read equals one complete line/JSON object/UTF-8 character, or ignoring EOF cleanup.
- **Flaky sleep-based synchronization**: Masking race conditions or ordering bugs with arbitrary delays instead of deterministic state coordination.

## Composing With Other Playbooks

- **+ `reference-port`**: Many real repos implement a clean core concept (like streaming tool-call accumulation or provider message normalization). Treat the reference repo as the behavioral oracle while using `core-concept.md`'s controlled-input and fault-injection tests to guarantee deep understanding rather than mechanical translation.
- **+ `language-feature`**: Use the concept's state machine as the real-world vehicle for learning the target language's idioms (e.g., SSE incremental parser as the vehicle for Rust `Stream` + `BytesMut` + `Pin`).
