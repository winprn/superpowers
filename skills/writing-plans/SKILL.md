---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Turn a spec into an implementation plan that an engineer with zero context could execute. For multi-item specs the plan is a **directory of per-item plan files** written by parallel subagents under a controller. For single-item specs it is a single file written inline. DRY. YAGNI. TDD. Frequent commits.

Assume implementers are skilled developers who know almost nothing about this toolset or problem domain, and who do not know good test design well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

## REQUIRED SUB-SKILL: decomposing-specs

Before doing anything else in this skill, you MUST have run `superpowers-winprn:decomposing-specs` against the spec and produced its decomposition table (ids, slugs, depends_on, touches, one-line summaries).

- If you have not run it yet, invoke it now and resume here only after its table is produced.
- If you ran it and produced a table, use that table verbatim as the manifest seed below. Do not re-enumerate items here — that is decomposing-specs's job, and re-doing it leads to the exact failure mode (collapsing multi-item specs into one plan) that decomposing-specs exists to prevent.

**Violating the letter of this requirement is violating its spirit.** Don't "quickly identify items inline" — that is the rationalization the sub-skill exists to block.

## Sequential Thinking

For dependency refinement and cross-plan consistency reasoning, use the sequential thinking MCP tool (`mcp__sequentialthinking__sequentialthinking`) to think through the problem step by step before committing to a structure. (Item enumeration itself has already happened in decomposing-specs.)

## Scope Check

If the spec covers multiple independent subsystems that should have been broken into sub-project specs during brainstorming, stop and suggest splitting before continuing. Each plan directory should produce working, testable software for **one** spec.

## Decide: Single File or Directory

Use the row count from the decomposing-specs table:

- **1 row** (with the single-item gate answered) → write a single plan file at `docs/superpowers/plans/YYYY-MM-DD-<topic>.md` using the inline flow below.
- **2+ rows** → use the directory flow with parallel plan-writer subagents. The decomposition table IS the manifest seed — copy its rows into the README.md frontmatter directly.

User preferences for plan location override these defaults.

---

## Directory Flow (multi-item specs)

### Layout

```
docs/superpowers/plans/YYYY-MM-DD-<topic>/
  README.md            # Manifest + index
  01-<item-slug>.md
  02-<item-slug>.md
  ...
```

### Step 1: Write the manifest from the decomposition table

Copy the rows from the decomposing-specs table into `README.md` frontmatter. Refine `depends_on` and `touches` if you discover gaps while writing, but do not re-enumerate items here. The frontmatter is the source of truth:

```markdown
---
spec: ../specs/YYYY-MM-DD-<topic>-design.md
items:
  - id: "01"
    slug: hook-installer
    file: 01-hook-installer.md
    depends_on: []
    touches:
      - src/hooks/install.ts
      - tests/hooks/install.test.ts
    status: pending
  - id: "02"
    slug: recovery-mode
    file: 02-recovery-mode.md
    depends_on: ["01"]
    touches:
      - src/hooks/recovery.ts
    status: pending
---

# <Feature> — Plan Index

Brief overview of the feature and how items compose. One-line summary per item with a link to its plan file.
```

**Field rules:**
- `id`: zero-padded ordinal (`"01"`, `"02"`, ...). Stable identifier.
- `slug`: kebab-case, derived from the item's title.
- `depends_on`: IDs of items that must finish their phase (writing for plan-writers, implementation for implementers) before this item proceeds. Infer from spec references and from `touches` overlap.
- `touches`: every file the plan will create or modify. Used by the executor to prevent parallel dispatch on conflicting paths. Be honest and complete here — under-declaration causes execution conflicts.
- `status`: lifecycle marker (`pending` → `writing` → `written` → `implementing` → `done`). Advisory only; treat git as the source of truth on resume.

**Cycle check:** if any item is transitively in its own `depends_on`, stop and escalate to the user with the graph.

### Step 2: Write per-item plans in topological waves

Repeat until all items are `status: written`:

1. **Compute the next wave** = `{ item | all item.depends_on are status:"written" }`.
2. **Dispatch one plan-writer subagent per item in the wave, in parallel.** This means a single message containing one Agent tool call per item. Do not sequence them.
3. Each subagent receives, via the prompt template at `./plan-writer-prompt.md`:
   - The spec excerpt for **only its item** (you extract it from the spec — do not make the subagent read the spec file).
   - The **full text** of every prerequisite plan file already written (so it can match types, signatures, and file paths).
   - Its manifest entry (`touches`, `depends_on`).
   - Project context: tech stack, key patterns, relevant existing files.
   - The plan task rules below (Plan Document Header, Task Structure, No Placeholders, Design Principles).
4. **Wait for the wave to complete.** Update `status` to `written` for each item in the wave.

If a wave is empty but items remain, you have a cycle the initial check missed — escalate.

### Step 3: Cross-plan consistency review

Run this yourself after all per-item plans are written. Do **not** dispatch a subagent — this is coordination work.

1. **Type and signature consistency.** A function defined in plan 01 and called in plan 03 must be spelled identically and have matching parameters/return types across all plans that reference it.
2. **Spec coverage.** Every spec requirement maps to a task in some plan. List any gap and add the task to the appropriate plan.
3. **Dependency correctness.** If plan 03 references something defined in plan 01, plan 03's `depends_on` must include `"01"`. Add missing edges.
4. **File ownership.** If two plans both `touches` the same path and both claim to create it, one must modify the other's output instead. Adjust the plans inline.

Fix issues in place. No re-review needed.

### Step 4: User review gate

> "Plan directory written and committed to `docs/superpowers/plans/YYYY-MM-DD-<topic>/`. Please review the manifest and item plans, and let me know if anything needs to change before execution."

Wait for approval before invoking execution.

---

## Single-File Flow (one-item specs)

When the spec has a single item, write the plan to `docs/superpowers/plans/YYYY-MM-DD-<topic>.md` using the structure below. Then run the self-review and present the user review gate.

---

## Plan Document Header (every plan file — single or per-item)

```markdown
# [Feature or Item Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## File Structure Section

Before defining tasks, list every file the plan will create or modify and what each is responsible for. This must match `touches` in the manifest entry for the item.

- Design units with clear boundaries and well-defined interfaces. Each file has one responsibility.
- Files that change together live together. Split by responsibility, not by technical layer.
- In existing codebases follow established patterns. If a file you're modifying has grown unwieldy, including a split in the plan is reasonable; don't unilaterally restructure unrelated code.

## Design Principles (Philosophy of Software Design)

All code in plan tasks MUST follow these. Violations are plan failures.

**Comment best practices.** Comments describe the *why*, not the *what*. Every module/class gets a high-level comment explaining its purpose. Interface comments describe what a method does, not how. Never write "add comments later".

**Doc comments for public members.** Inline in plan code blocks:
- **C#:** `/// <summary>` XML doc comments
- **TypeScript/JavaScript:** JSDoc (`/** */`)
- **Python:** docstrings (`"""..."""`)

**Deep modules.** Simple interfaces hiding complex implementations. Favor fewer powerful abstractions over many shallow ones. If a module's interface is as complex as its implementation, redesign it.

**Define errors out of existence.** Design APIs so invalid input can't be passed. Typed enums over arbitrary strings; builder patterns that enforce required fields at compile time. Extensive input validation is a smell — reconsider the API shape.

## Bite-Sized Task Granularity

Each step is one action, 2-5 minutes:
- "Write the failing test" — step
- "Implement the minimal code to make the test pass" — step
- "Refactor if needed" — step

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 3: Refactor if needed**

Extract common patterns, improve names, remove duplication. Keep tests green.
````

## No Placeholders

Every step must contain the actual content an engineer needs. These are plan failures — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — engineers may read tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always.
- Complete code in every step — if a step changes code, show the code.
- DRY, YAGNI, TDD.

## Per-Plan Self-Review

Each plan file (whether single or per-item) gets reviewed by whoever wrote it before being marked done:

1. **Spec coverage** for this plan's slice. Every requirement in the assigned spec excerpt maps to a task. List and fill any gap.
2. **Placeholder scan.** Search for the patterns in "No Placeholders" above and fix.
3. **Type consistency within the plan.** Names, signatures, and properties are consistent across tasks in this file.

Fix inline. No re-review.

The directory flow's Step 3 cross-plan review extends this across plans — but the per-plan review still runs for each file first.

## Execution Handoff

After the user approves the plan:

**For a plan directory:**

> "Plan complete. Two execution options:
>
> **1. Subagent-Driven (recommended)** — `superpowers:subagent-driven-development` reads the manifest and dispatches implementer subagents in parallel waves with per-item review.
>
> **2. Sequential** — `superpowers:executing-plans` processes items in topological order without parallelism (use when subagents aren't available).
>
> Which approach?"

**For a single-file plan:**

> "Plan complete. Two execution options:
>
> **1. Subagent-Driven (recommended)** — fresh subagent per task + two-stage review.
>
> **2. Inline Execution** — execute tasks in this session with checkpoints.
>
> Which approach?"

Invoke the chosen skill.
