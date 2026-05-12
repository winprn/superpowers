---
name: decomposing-specs
description: Use when you have an approved spec and are about to write an implementation plan - enumerates implementable items before any plan file exists, preventing the default failure of collapsing a multi-item spec into one plan
---

# Decomposing Specs Into Plan Items

## Overview

Before any plan file is written, enumerate the **items** in the spec. An item is an implementable unit that produces working, testable software on its own.

**The failure this skill prevents:** Reading a spec and silently deciding "this is one feature, one plan file." That collapse erases parallelism, hides dependencies, and forces a single subagent to hold the entire design in its head while writing.

**The default is wrong.** A spec that *feels* like "one thing" almost always contains 2+ items. Your job here is to **find them** before the writing-plans skill commits to a layout.

**Announce at start:** "I'm using the decomposing-specs skill to enumerate plan items."

## When to Use

- Always, when transitioning from an approved spec to plan writing.
- Invoked by `superpowers-winprn:writing-plans` before its "Decide: Single File or Directory" step.

**When NOT to use:**
- Spec is a one-line bugfix or a single function change with no surface area beyond itself. (Even then, run the checklist — it takes 60 seconds.)

## The Iron Rule

```
PRESUME MULTIPLICITY.
Single-item classification requires written justification, not silence.
```

You do not get to land on "1 item" by default. You land on "1 item" only after the enumeration below produces exactly one row **and** you can answer the single-item gate questions in writing.

## Enumeration Procedure

Use the sequential thinking MCP tool (`mcp__sequentialthinking__sequentialthinking`) for this. Do not skip it — the whole point of this skill is to slow you down at the moment your instinct is to move fast.

### Step 1: Read the spec end-to-end

No skimming. No "I already know what this says." Read it.

### Step 2: Extract candidate items

Write a flat list. Every one of the following is a **candidate item** — include it now, prune later:

- Every numbered top-level section in the spec
- Every distinct feature, capability, or user-facing behavior mentioned
- Every new file, module, or component the spec names
- Every distinct concern: data model, API surface, UI, CLI, hook, migration, config, docs
- Every "and" or "also" that joins what might be separate work
- Every existing component the spec says will be **modified** (modification of an existing thing is often its own item)

Err on the side of including. You will merge later.

### Step 3: Apply the item test to each candidate

For each candidate, ask all four:

1. **Standalone value:** Could a developer ship this alone and have it do something useful or testable?
2. **Clear boundary:** Are the files it touches enumerable without overlapping every other candidate's files?
3. **Independent test surface:** Can it be tested without the other candidates being implemented?
4. **Separable review:** Could a reviewer review it on its own merits?

If **any** answer is yes for any pair of candidates being considered for merging, keep them separate. Merge only when **all four** are no for the merged result.

### Step 4: Infer dependencies

For each surviving item, list which other items must exist first. Dependencies come from:

- Explicit spec references ("item B uses the X defined in item A")
- File overlap in `touches` (two items modifying the same file → one depends on the other)
- Type/interface usage (consumer depends on producer)

Cycles are bugs. If you find one, the decomposition is wrong — split or merge until the graph is a DAG.

### Step 5: Identify file touches

For each item, list every file it will create or modify. Be honest and complete — under-declaration breaks parallel execution.

### Step 6: Produce the decomposition table

Output this table inline in your response **before** invoking writing-plans:

```markdown
## Decomposition

| id | slug | depends_on | touches | one-line summary |
|----|------|-----------|---------|------------------|
| 01 | hook-installer | — | src/hooks/install.ts, tests/hooks/install.test.ts | Install the pre-commit hook |
| 02 | recovery-mode | 01 | src/hooks/recovery.ts | Recover from a failed hook run |
```

This is the seed for the writing-plans manifest. writing-plans consumes it directly — do not re-decompose there.

## Single-Item Gate

If your table has exactly one row, you must answer **all** of these in writing before proceeding:

1. What is the single user-facing capability this spec adds? (One sentence. If you write two, you have two items.)
2. Why is every file change required for that one capability? Name each file and tie it to the capability.
3. What would have to be true about the spec for it to have a second item? If you can describe a plausible second item the spec is silent on, surface it back to the user before continuing — the brainstorming may be incomplete.

Skip this gate and you are violating the skill.

## Red Flags — STOP and Re-Decompose

These thoughts mean your enumeration is wrong:

| Thought | Reality |
|---------|---------|
| "It's all one feature" | Features have parts. Name them. |
| "These pieces are tightly coupled" | Coupling is not unity. Items can depend on each other and still be separate items. |
| "Splitting adds overhead" | Parallel plan-writing reduces wall time. The overhead is in your head. |
| "The spec only has one numbered section" | Sections are a hint, not the rule. Re-read the prose. |
| "I'll let writing-plans decide" | writing-plans trusts your decomposition. It will not re-do this. |
| "Two items but they share types" | Shared types live in one item; the other depends on it. Still two items. |
| "Frontend and backend of the same feature" | That's two items. Almost always. |
| "Migration + the code that uses the new schema" | Two items. Migration ships first. |
| "Feature + its tests" | One item. Tests live with the code they cover. Do not split this way. |
| "Feature + docs for the feature" | One item, unless the docs are a substantial standalone deliverable. |

## Common Mistakes

**Splitting by technical layer instead of by capability.** "Models, then services, then controllers" is three items that have no standalone value. Split by user-facing capability instead; each item then spans whatever layers it needs.

**Splitting tests off into their own item.** Tests live with the code they cover. TDD is per-item, not a separate item.

**Hiding a second item inside Task N of a single plan.** If you are tempted to write Task 5 as "now also build the recovery flow," that recovery flow is a second item. Promote it.

**Treating "modify existing file X" as not an item.** Modifying an existing module to add behavior is an item if the modification has standalone value and a clear boundary. Most non-trivial features include such items.

## Handoff to writing-plans

After producing the decomposition table:

> "Decomposition complete: N items. Invoking writing-plans to write the plan {directory if N≥2 / file if N=1}."

writing-plans will:
- Use your table verbatim as the manifest seed (ids, slugs, depends_on, touches).
- Skip its own "identify items" reasoning — your output replaces it.
- Dispatch one plan-writer subagent per item in topological waves.

If you produced a single-item decomposition with the gate answers, writing-plans goes to the single-file flow. Otherwise it goes to the directory flow.

## The Bottom Line

The agent's default move is to read a spec, feel like it understands it, and write one plan file. That default is wrong for almost every multi-feature spec. This skill exists to make you enumerate before you commit — because once a plan file exists, no one goes back and splits it.

Enumerate first. Justify single-item in writing. Hand a table to writing-plans.
