---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load a plan (single file or directory manifest), review critically, execute all tasks in dependency order, report when complete.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

**Note:** Tell your human partner that Superpowers works much better with access to subagents. If subagents are available, use `superpowers:subagent-driven-development` instead — it parallelizes plan directories across waves. This skill processes everything sequentially.

## Detect the Plan Shape

- **Single plan file** (`docs/superpowers/plans/YYYY-MM-DD-<topic>.md`) → execute its tasks in order.
- **Plan directory** (`docs/superpowers/plans/YYYY-MM-DD-<topic>/`) with `README.md` manifest → process each item's plan in **topological order** based on the manifest's `depends_on` graph. No parallelism in this skill — that's `subagent-driven-development`'s job.

## The Process

### Step 1: Load and Review

**Single file:**
1. Read the plan file.
2. Review critically — note any questions or gaps.

**Directory:**
1. Read `README.md` and parse the manifest frontmatter.
2. Build the dependency graph from `depends_on`. Detect cycles; escalate if any.
3. Read each per-item plan file.
4. Review critically across all plans — note any questions, gaps, or cross-plan inconsistencies (signatures, file ownership).

If concerns: raise them with your human partner before starting.
If no concerns: create TodoWrite (one entry per task across all plans, in topological order) and proceed.

### Step 2: Execute Tasks

**Single file:** execute each task in order. Mark in_progress, follow steps exactly, run verifications, mark completed.

**Directory:** process items in topological order (Kahn's algorithm on the manifest graph; ties broken by `id` ascending). For each item:
1. Update its manifest `status` to `implementing`.
2. Execute every task in that item's plan in order.
3. Run any verifications the plan specifies. If they fail, stop and ask — don't guess.
4. Update its manifest `status` to `done`.

### Step 3: Complete Development

After all items reach `done`:

- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use `superpowers:finishing-a-development-branch`.

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker (missing dependency, test fails, instruction unclear).
- Plan has critical gaps preventing starting.
- An instruction is unclear or contradicts another plan.
- Verification fails repeatedly.
- The manifest graph has a cycle.

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback.
- Fundamental approach needs rethinking.
- Mid-execution discovery invalidates a downstream item's plan (e.g., signature changed) — pause and raise before continuing.

**Don't force through blockers** — stop and ask.

## Remember

- Review the plan critically first.
- Follow plan steps exactly.
- Don't skip verifications.
- For directories: respect the dependency graph; don't reorder items.
- Reference skills when the plan says to.
- Stop when blocked, don't guess.
- Never start implementation on main/master branch without explicit user consent.

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** — REQUIRED: set up isolated workspace before starting.
- **superpowers:writing-plans** — creates the plan (single file or directory) this skill executes.
- **superpowers:finishing-a-development-branch** — completes development after all tasks.

**When subagents are available:**
- **superpowers:subagent-driven-development** — parallel wave execution for plan directories, per-item review checkpoints.
