# build-to-learn

An agent skill that turns *"I want to learn a package, language feature, core concept, or port a reference codebase"* into a **progressive, hands-on, verifiable coding project** where the learner writes the core code stage by stage.

## Install

Install with [vercel-labs/skills](https://github.com/vercel-labs/skills):

```bash
npx skills add Dailiduzhou/build-to-learn
```

## How It Works

- **One evolving runnable project**: Every stage adds one observable behavior and one primary concept to the same codebase, paired with just-in-time stage docs under `.learn/stages/` and a single progress tracker at `.learn/PROGRESS.md`.
- **4 composable playbooks**: Supports learning a **library/package**, **language feature**, **core concept**, or **porting a reference codebase** (including cross-language ports with explicit source-vs-target language comparisons).
- **Fading scaffolding & strict no-spoiler coaching**: The agent prepares project setup, fixtures/mocks, failing acceptance tests, and early `todo!()` stubs—never silently writing your core code. Debugging follows a 4-level hint ladder (`L1 Phenomenon → L2 Pointer → L3 Mechanism → L4 Local Pseudocode`).
- **Dual-gate verification**: Checking a stage runs tests **and** audits `git diff` to catch concept bypasses, then unlocks a short Transfer Challenge (recorded separately from Functional acceptance; skippable with deferred re-checks).

## Usage Examples

- **Start a roadmap**: *"I'm familiar with TypeScript and basic Rust ownership. Help me learn Rust `Stream`/`Pin`/`Poll` by porting `~/code/pi/packages/ai` step by step—don't write the core code for me."*
- **Check your implementation**: *"I finished Stage 1 and `cargo test` passes—please check my implementation."*
- **Get unstuck**: *"I'm stuck on an `Unpin` compiler error in Stage 2, give me a hint without rewriting my code."*
- **Resume later**: *"Continue my learning progress from `.learn/PROGRESS.md`."*

## Repository Structure

```text
.
├── SKILL.md                              # Core workflow, entrypoints, and guardrails
└── references/
    ├── templates.md                      # .learn/PROGRESS.md and stage card templates
    ├── verification-and-hints.md         # Fading scaffolding, anti-bypass audit, 4-level hints, transfer protocol
    └── playbooks/
        ├── library.md                    # Package / SDK / framework learning arc
        ├── language-feature.md           # Type system, ownership, async, runtime semantics
        ├── core-concept.md               # Protocols, algorithms, systems mechanisms
        └── reference-port.md             # Vertical-slice porting & cross-language lens
```

## License

See [LICENSE](./LICENSE).
