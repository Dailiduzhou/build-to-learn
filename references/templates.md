# Templates for `.learn/` Workspace Files

Write all generated files in the **learner's language** (translate section headings and field labels naturally into Chinese when conversing in Chinese, or keep English when conversing in English). Preserve the exact structural information below so any future session can resume cleanly with zero lost context.

---

## 1. `.learn/PROGRESS.md` Template

`.learn/PROGRESS.md` is the **single source of truth** for the roadmap overview, current stage pointer, mastery evidence, stuck points, and deferred re-checks. Do not create a separate `ROADMAP.md` that could drift out of sync.

```markdown
# Learning Progress: <Topic / Project Title>

## Overview
- **End Goal**: In <context/situation>, independently <concrete capability / finished project>.
- **Playbooks**: Primary: `<library | language-feature | core-concept | reference-port>` | Secondary: `<none or playbook>`
- **Learner Background**: <relevant experience and transferable skills>
- **Skipped Prerequisites**: <topics skipped + why/evidence, or "None">
- **Reference Source**: <path/URL to reference codebase, or "None">
- **Repo Mode**: `<standalone learning repo | existing repo (branch: learn/<topic>)>`

## Current Pointer
- **Current Stage**: `01` — `<Stage Title>` ([01-<slug>.md](stages/01-<slug>.md))
- **Status**: `in_progress` <!-- in_progress | functional_passed_awaiting_transfer | ready_for_next_stage | completed -->
- **Scaffolding Tier**: `early` <!-- early | mid | late -->
- **Diff Anchor**: `<commit sha or "initial scaffold files: ...">`
- **Next Concrete Action**: <one unambiguous next step + exact command to run>

## Roadmap & Mastery Log

> Future stages list only title, target capability, and dependencies. Detailed stage cards are generated one at a time after the previous stage closes.

| Stage | Title | Target Capability ("In X, independently Y") | Dependencies | Functional | Transfer | Evidence / Card |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 01 | <Minimal Runnable Slice> | <capability> | None | `pending` | `pending` | [01-<slug>.md](stages/01-<slug>.md) |
| 02 | <Next Observable Behavior> | <capability> | 01 | — | — | *(planned)* |
| 03 | <Next Concept / Edge Case> | <capability> | 02 | — | — | *(planned)* |

### Verified Evidence Log
*(Append one concise block per completed or skipped stage.)*

- **Stage 01 — <Title>**
  - **Functional**: `pass` — `<exact command>` passed; diff verified `<anti-bypass invariant checked>`.
  - **Transfer**: `pass` | `skipped` — `<variation tested + observed result, OR reason skipped & deferred target>`.
  - **Assisted Points**: `<None, or specific sub-part where solution was revealed>`.

## Stuck Points & Deferred Re-checks
- **Active Stuck Points**:
  - *(Record recurring errors, highest hint level used L1–L4, and root misconception so future stages reinforce it.)*
- **Deferred Re-checks** *(from skipped transfers or assisted solutions)*:
  - *(Example: `[From Stage 01] Handling mid-stream UTF-8 split across chunks — revisit in Stage 02/03.`)*
```

### Status & Recording Rules
- Always record **Functional** (`pending` | `pass` | `skipped (verified)`) and **Transfer** (`pending` | `pass` | `skipped`) in **separate columns/fields**—never collapse them into a single "done" checkmark.
- Only list a stage as `skipped (verified)` when backed by existing code evidence or a passed diagnostic check; record that evidence in the Verified Evidence Log.
- Never pre-create `.learn/stages/02-*.md`, `03-*.md`, etc. during initialization. Generate each stage card just-in-time.

---

## 2. `.learn/stages/NN-<slug>.md` Template

This template merges the 6 essential stage-card items (learning goal, new behavior, code entrypoints, learner implementation scope, acceptance command & expected output, transfer challenge) with the *Problem → Concept → Build It → Compare/Use It → Verify/Transfer* instructional arc.

```markdown
# Stage <NN>: <Stage Title>

> **Scaffolding Tier**: `<early | mid | late>` | **Primary Concept**: `<one primary concept>` | **Playbook Lens**: `<e.g., reference-port + language-feature>`

## 1. The Problem & Learning Goal
- **Why this stage exists now**: <What concrete gap, failure mode, or missing capability motivates this step?>
- **Learning Goal**: In <situation>, you will be able to independently <verifiable skill>.
- **Revisited from earlier stages** *(include only if pulling in a deferred re-check or stuck point)*: <brief link>

## 2. Core Concept
<Concise, sharp mental model of the primary concept—explain the mechanics, constraints, and invariants without spoiling drop-in solution calls or step-by-step algorithm pseudocode.>

### Source vs. Target Lens *(include when porting or transferring from a familiar language/framework)*
- **Reference Implementation**: `<path/to/reference/file#Lstart-Lend>`
- **How the source does it**: <idiom, runtime assumption, or abstraction in source language>
- **How the target language/runtime differs**: <ownership, typing, async/lifecycle, or error-handling shift to notice while coding>

## 3. New Observable Behavior
When this stage is complete and you run the project, what visibly changes compared to the previous stage:
- <Concrete observable behavior 1>
- <Concrete observable behavior 2 (if applicable)>

## 4. Code Entrypoints & Implementation Scope

### What You Implement (Learner Owns)
- `<path/to/target_file>` (`<symbol/function/struct>`): <exact responsibility to implement>
- **Design freedom** *(matches scaffolding tier)*: <e.g., "Early tier: signature stub is prepared; focus on the core logic." OR "Mid tier: black-box contract test is fixed; design internal helper types/modules yourself.">

### What Is Prepared for You (Scaffold & Harness)
- `<path/to/test_file>`: failing acceptance tests defining the contract
- `<path/to/mock_or_fixture>`: deterministic inputs / mock server / fixtures
- `<config/boilerplate>`: dependencies already wired

## 5. Acceptance Command & Anti-Bypass Criteria
- **Run command**:
  ```bash
  <exact runnable test/run command>
  ```
- **Expected result**: <specific passing tests / observable output>
- **Anti-bypass invariant** *(what must be true in your code, not just test output)*:
  - <e.g., "Events must be emitted incrementally as chunks arrive—collecting the whole response body into a buffer before parsing bypasses the streaming invariant.">

## 6. Transfer Challenge *(Unlocked after Functional Pass)*
Once the acceptance command passes and diff review confirms the invariant:
- **Variation**: <one focused 10–15 min change: altered input boundary, injected failure/cancellation, new constraint, or edge-case test>
- **How it verifies transfer**: <exact test command or observable proof + optional 1-sentence mechanism question>
- *(You may complete this to earn full mastery, or explicitly ask to skip—skipping will be recorded separately and revisited in a later stage.)*

## 7. Reference Pointers *(Optional)*
- <Official docs section / RFC / reference source file path—pointers only, no spoiler answers>
```
