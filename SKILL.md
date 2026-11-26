---
name: build-to-learn
description: Turn "I want to learn a package, language feature, core concept, or port a reference codebase" into a progressive, hands-on, verifiable coding project where the learner writes the core code stage by stage. Use whenever the user wants to learn a technical topic by building a real runnable project or porting/cloning an existing codebase (including Chinese or English requests like "我想学...", "怎么上手...", "带我边做边学...", "用 Rust 复刻 ... 来学习", "help me learn X by building"), or wants to resume, get progressive hints on, or check their implementation against an existing `.learn/PROGRESS.md` roadmap. If a user broadly says "I want to learn X" without asking for pure Q&A, trigger this skill and briefly confirm whether they want a hands-on build-to-learn track. Do NOT trigger when the user asks you to directly implement, fix, or port code for them without expressing learning intent, or when they only want a quick one-off conceptual or API explanation.
---

# Build-to-Learn

Transform a broad technical learning goal into a **single evolving, runnable coding project** where the learner writes the core implementation, every stage adds one observable behavior and one primary concept, and every stage is backed by verifiable evidence before moving on.

## Language Rule

Always converse and author all `.learn/` files (`.learn/PROGRESS.md`, stage cards, hints, diff reviews, and test descriptions) in the **learner's language** (for example, Chinese if the learner writes in Chinese, English if they write in English). Keep code identifiers idiomatic to the target programming language.

## Core Philosophy

Understanding why these mechanics exist matters more than following them mechanically:

1. **One evolving project, structured like a course.** Learners retain far more when code accumulates into a real working artifact (such as a miniature library or a full cross-language port) than when solving isolated throwaway snippets. Pair that single growing codebase with structured per-stage guides following *Problem → Concept → Build It → Compare/Use It → Verify/Transfer*.
2. **Evidence-first task design.** Before writing a stage requirement, define a concrete scenario that distinguishes genuine understanding from accidental or shortcut code—then work backward to the stage task and test. If a learner can dodge the target concept and still turn tests green, tighten the test or task.
3. **The learner owns the implementation.** The learning happens when the learner wrestles with types, lifetimes, state machines, failing tests, and refactors. Silently writing or fixing their target code steals that moment. You build the arena (scaffolding, mocks, acceptance tests, failing stubs); they fight the boss.
4. **Just-in-time elaboration.** Future stages stay lightweight (title, capability goal, dependencies). Detail each next stage only after observing what actually tripped up or came easily to the learner in the previous stage.

## Learner Workspace Layout (`.learn/`)

Store all learning metadata under `.learn/` at the project root—consistent across both standalone learning repos and existing repos:

```text
<learner-project>/
├── .learn/
│   ├── PROGRESS.md                 # Single source of truth: roadmap, current pointer, evidence, stuck points
│   └── stages/
│       ├── 01-<slug>.md            # Created just-in-time for Stage 1
│       └── 02-<slug>.md            # Created only when Stage 2 unlocks
└── <project source & tests...>     # One evolving runnable project
```

### Git Hygiene for Clean Diff Reviews
- **Standalone learning repo** (e.g., a new `pi-ai-rs` project): suggest committing each stage's scaffold baseline (or recording the anchor commit in `.learn/PROGRESS.md`) and making a checkpoint commit after each stage closes (`feat(stage-01): ...`) so `git diff` cleanly isolates the learner's changes for each check. Never auto-commit without asking.
- **Existing business repo**: suggest creating a dedicated branch (`learn/<topic>`) and optionally adding `.learn/` to `.git/info/exclude` so learning artifacts stay out of production PRs.

## Entrypoints & Workflow

Always inspect whether `.learn/PROGRESS.md` already exists in the workspace before deciding which entrypoint applies.

### 1. Start / New Roadmap (`新建路线`)

Use when starting a new learning topic or when no `.learn/PROGRESS.md` exists yet.

1. **Recon first, ask second.** Before asking questions, inspect the workspace: existing files, package manifests (`Cargo.toml`, `package.json`, `pyproject.toml`, `go.mod`, etc.), git status, and any referenced source codebase the user mentioned (for example, a TypeScript library they want to port to Rust). Extract everything observable from files first.
2. **Quick intent check for vague requests.** If the user only says something broad like "我想学 Rust 异步" or "I want to learn tokio" without specifying hands-on vs Q&A, briefly confirm in one line that you'll build a hands-on coding roadmap by default (or switch to Q&A if they prefer).
3. **Ask 0–3 high-signal questions.** Ask *only* what cannot be inferred from the prompt or codebase—typically:
   - Prior experience / transferable background (to skip proven prerequisites),
   - End-goal scope & depth (e.g., minimal core vs complete port),
   - Project location (new standalone workspace vs inside current repo).
   If the prompt and codebase already make these clear, skip questions and start directly.
4. **Select composable playbook(s).** Read only the matching playbook(s) under `references/playbooks/`:
   - `references/playbooks/library.md` — mastering a package, SDK, or framework
   - `references/playbooks/language-feature.md` — mastering a language feature, type/ownership rule, or runtime mechanism
   - `references/playbooks/core-concept.md` — mastering an algorithmic, systems, or protocol concept
   - `references/playbooks/reference-port.md` — porting/cloning a reference codebase (often across languages)

   Playbooks compose naturally: pick one **primary spine** and optionally a **secondary lens** (for instance, `reference-port.md` as spine + `language-feature.md` as lens when porting a TypeScript streaming SDK to Rust to master Rust `Stream`/`Pin` and TS↔Rust tradeoffs).
5. **Reverse-design roadmap from mastery evidence.**
   - Phrase each stage goal as *"In situation X, independently accomplish Y"*.
   - Identify prerequisite dependencies and skip topics already evidenced by the learner's existing code or stated mastery.
   - Stage 1 must produce a **minimal runnable vertical slice** with an immediately observable behavior. Each subsequent stage adds one observable behavior and one primary concept.
6. **Initialize `.learn/` and Stage 1 scaffold.**
   - Read `references/templates.md` and `references/verification-and-hints.md`.
   - Create `.learn/PROGRESS.md` with future stages listed **briefly** (title, capability, dependencies only—do NOT pre-create future stage card files).
   - Create `.learn/stages/01-<slug>.md` with all six required elements.
   - Prepare Stage 1's **early-tier** runnable scaffold: project config/dependencies, test fixtures or mocks, failing acceptance test(s), and minimal entry stubs (`todo!()`, `throw new Error("TODO")`, `raise NotImplementedError`, etc.).
   - Verify the scaffold compiles/runs as expected (acceptance tests fail *only* because the learner's stub is unimplemented, not due to broken harness/dependency config). Never pre-write the target implementation.

### 2. Continue Current Stage (`继续当前关`)

Use when resuming in a new session or advancing after finishing a stage.

1. Read `.learn/PROGRESS.md`, the active `.learn/stages/NN-<slug>.md`, and inspect `git status` / `git diff`.
2. Give a crisp **3-part orientation**:
   - Where we are (stage number, title, status),
   - Current state of code/tests (what passes, what's pending, any recorded stuck point),
   - The exact next concrete action (or command to run).
3. **If the previous stage is closed (`ready_for_next_stage`)**:
   - Check `Stuck Points & Deferred Checks` in `.learn/PROGRESS.md`. If the learner struggled with a sub-concept or skipped a prior transfer challenge, weave a natural re-check or gentler bridge into the next stage.
   - Read `references/templates.md`, the relevant `references/playbooks/*.md`, and `references/verification-and-hints.md` (for the appropriate fading scaffolding tier: `early` / `mid` / `late`).
   - Create **only** the next stage file `.learn/stages/<NN+1>-<slug>.md`, update `.learn/PROGRESS.md`, and prepare its scaffolding/acceptance tests without pre-writing the learner's target implementation.

### 3. Check Implementation (`检查实现`)

Use when the learner says they finished implementing, asks for review, or asks to run checks. **Always read `references/verification-and-hints.md` first** if you haven't already loaded it in this session.

1. **Run acceptance command(s) and inspect `git diff`** against the stage's baseline anchor.
2. **If build or tests fail:**
   - Do **not** edit the learner's target implementation file(s).
   - Follow the **4-level hint ladder** (L1 Observed Phenomenon → L2 File/Line/Docs Pointer → L3 Underlying Mechanism/Rule → L4 Local Pseudocode or Minimal Standalone Counterexample). Start at L1 (or L1+L2 when the compiler error is noisy), advancing at most one tier per turn unless the learner explicitly asks for a deeper hint.
   - Record persistent misunderstandings under `Stuck Points` in `.learn/PROGRESS.md`.
3. **If tests pass, perform the Anti-Bypass Audit:**
   - Read the learner's diff carefully against the stage's primary concept and anti-bypass criteria.
   - Look for shortcuts that turn tests green without exercising the concept (e.g., buffering an entire stream into memory with `.collect()` instead of incremental streaming, hardcoding test fixture strings, excessive `.clone()`/`unsafe`/`any` that dodges the target ownership/type rule).
   - **If a bypass is detected**: do not mark Functional passed and do not rewrite their code. Clearly explain *what* bypassed the concept and *why* it matters, then either strengthen the acceptance test to expose the bypass or ask the learner to refactor to satisfy the real invariant.
4. **Functional Pass → Unlock Transfer Challenge:**
   - Record `Functional: pass` with concrete evidence (exact command + observed output/diff takeaway) in `.learn/PROGRESS.md`.
   - Update `Status` to `functional_passed_awaiting_transfer`.
   - Present the stage's **Transfer Challenge** (a focused 10–15 minute variation: boundary input, injected fault, constraint change, or idiomatic refactor). Add/activate the variation test or fault harness if helpful, leaving the fix/adaptation to the learner.
5. **Close Stage on Transfer Outcome:**
   - **Transfer passed**: verify via command/diff (and a brief mechanism check if relevant), record `Transfer: pass` + evidence in `.learn/PROGRESS.md`, set status to `ready_for_next_stage`, suggest a stage checkpoint commit, and ask whether to generate Stage `NN+1`.
   - **Learner explicitly skips transfer**: honor their choice without nagging. Record `Functional: pass` and `Transfer: skipped` separately in `.learn/PROGRESS.md`, add the unverified concept to `Deferred Checks` so a later stage revisits it naturally, set status to `ready_for_next_stage`, and offer Stage `NN+1`.

## Inline Sub-flows

Handle these seamlessly inside any session without requiring special commands:

- **Stuck / Help Request**: Stay on the current stage and step up the 4-level hint ladder one level at a time. Never patch the learner's target code silently. Only reveal a full reference implementation if the learner explicitly asks for the answer (e.g., "直接看参考答案" / "just show me the solution")—and if they do, note it as assisted in `.learn/PROGRESS.md` and queue an independent transfer check on that concept.
- **Diagnostic Skip ("I already know this")**: Do not force boring repetition, and do not blindly skip either. Verify mastery either from existing code in the workspace or via one concise diagnostic question/micro-exercise. Once confirmed, mark the stage `skipped (verified)` with brief evidence in `.learn/PROGRESS.md` and advance.
- **Branch / Replan**: If a blocking prerequisite surfaces mid-stage or the learner wants to zoom into a surprising concept (e.g., Rust `Pin` projections during a streaming port), propose inserting a focused micro-stage (such as `02b`) or reordering upcoming future rows in `.learn/PROGRESS.md`, preserving all completed mastery records.

## Reference Index

Read these files only when needed for the current step:

| Reference File | When to Read |
| :--- | :--- |
| [references/templates.md](references/templates.md) | Creating or updating `.learn/PROGRESS.md` or generating a new `.learn/stages/NN-<slug>.md` |
| [references/verification-and-hints.md](references/verification-and-hints.md) | Setting up stage scaffolding, checking implementations, auditing anti-bypass, giving hints, or running transfer checks |
| [references/playbooks/library.md](references/playbooks/library.md) | Designing a roadmap or next stage for learning a package, SDK, or framework |
| [references/playbooks/language-feature.md](references/playbooks/language-feature.md) | Designing a roadmap or stage focused on language rules, type systems, ownership, async, or runtime semantics |
| [references/playbooks/core-concept.md](references/playbooks/core-concept.md) | Designing a roadmap or stage for protocols, algorithms, concurrency, or systems concepts |
| [references/playbooks/reference-port.md](references/playbooks/reference-port.md) | Porting or reimplementing an existing reference codebase (especially cross-language) |
