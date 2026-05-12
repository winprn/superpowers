# Plan-Writer Subagent Prompt Template

Use this template when dispatching a subagent to write **one item's plan file** in the directory flow.

```
Task tool (general-purpose):
  description: "Write plan for item NN: [item slug]"
  prompt: |
    You are writing the implementation plan for **one item** in a larger spec.
    Your only deliverable is the file `<plan_dir>/NN-<slug>.md`.

    ## Item

    - **ID:** NN
    - **Slug:** [slug]
    - **Manifest entry (touches, depends_on):**

      ```yaml
      [paste this item's entry from the manifest]
      ```

    ## Spec Excerpt (your scope)

    [PASTE the section of the spec that defines this item. Do NOT include the entire spec.
    The implementer of this plan must be able to deliver the item from this excerpt alone.]

    ## Prerequisite Plan Files (already written)

    These plans were written in earlier waves. Use the exact function names, file paths,
    types, and signatures they define — do not rename or rework them.

    ### `NN-<prereq-slug>.md`

    [PASTE full text of prerequisite plan]

    [Repeat for each prerequisite in depends_on.]

    ## Project Context

    - **Tech stack:** [...]
    - **Established patterns to follow:** [...]
    - **Existing files this item relates to:** [paths + one-line purpose]
    - **Worktree / working directory:** [path]

    ## What to Write

    Write a complete plan file at `<plan_dir>/NN-<slug>.md` following these rules:

    1. **Header** (exactly this format):

       ```markdown
       # [Item Name] Implementation Plan

       > **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

       **Goal:** [one sentence]

       **Architecture:** [2-3 sentences]

       **Tech Stack:** [key tech]

       ---
       ```

    2. **File Structure section** listing every file you'll create or modify.
       This MUST match the `touches` field in your manifest entry. If you discover
       you need to touch a file not in `touches`, stop and report — don't silently
       expand scope.

    3. **Tasks** using the bite-sized structure (Files / Steps / code blocks).
       Each step is 2-5 minutes of work. Show actual code in every code step.

    4. **TDD throughout.** Write the failing test step before the implementation step.

    ## Design Principles (non-negotiable in your code blocks)

    - **Comments describe the why, not the what.** Module/class high-level comments
      and interface comments are part of the code, not a follow-up.
    - **Public members get doc comments inline** (XML doc for C#, JSDoc for TS/JS,
      docstrings for Python).
    - **Deep modules:** simple interfaces, complex implementations hidden. No shallow
      wrappers.
    - **Errors out of existence:** API shapes that make invalid input unrepresentable
      beat APIs that validate and throw. Use typed enums, builder patterns.

    ## No Placeholders

    These are failures — never write them:
    - "TBD", "TODO", "implement later", "fill in details"
    - "Add appropriate error handling" / "add validation" / "handle edge cases"
    - "Write tests for the above" (no actual test code)
    - "Similar to Task N" (repeat the code — implementers may read out of order)
    - Steps that describe what to do without showing how
    - References to types, functions, or methods not defined in any task

    ## Cross-Plan Consistency

    Your plan will be reviewed against other plans for type/signature drift.
    Before reporting back:

    - Names you reference from prerequisite plans must match exactly.
    - Names you introduce here will be referenced by later plans — choose them well
      and use them consistently across all your tasks.

    ## Self-Review (before reporting back)

    1. **Spec coverage:** every requirement in your spec excerpt maps to a task.
    2. **Placeholder scan:** none of the patterns above appear in your plan.
    3. **Internal consistency:** function names, parameters, file paths used in
       later tasks match what you defined in earlier tasks.

    Fix issues in place.

    ## Report Format

    When done, report:
    - **Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
    - Path to the plan file you wrote
    - Brief summary: number of tasks, files this item touches
    - Any concerns (scope creep, missing context, ambiguous spec)

    Use BLOCKED if the spec excerpt is too vague to write a complete plan.
    Use NEEDS_CONTEXT if you need information that wasn't provided.
    Use DONE_WITH_CONCERNS if you produced the plan but flagged doubts.
```
