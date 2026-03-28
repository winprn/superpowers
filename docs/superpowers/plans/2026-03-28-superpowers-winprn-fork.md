# Superpowers-winprn Fork Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fork superpowers with sequential thinking MCP integration, Philosophy of Software Design principles, simplified plan templates, and rebranded plugin identity.

**Architecture:** Targeted edits to 2 skill markdown files and 3 config/manifest JSON files. No structural changes — purely content modifications to existing files.

**Tech Stack:** Markdown, JSON

**Spec:** `docs/superpowers/specs/2026-03-28-superpowers-winprn-fork-design.md`

---

## Task 1: Add Sequential Thinking MCP to Brainstorming Skill

**Files:**
- Modify: `skills/brainstorming/SKILL.md:139-146` (Key Principles section)
- Modify: `skills/brainstorming/SKILL.md:117-124` (Spec Self-Review section)

- [ ] **Step 1: Add sequential thinking MCP to Key Principles**

In `skills/brainstorming/SKILL.md`, add a new bullet to the "Key Principles" section (after line 146):

```markdown
- **Sequential thinking for complexity** - For complex design decisions, trade-off analysis, or multi-step reasoning, use the sequential thinking MCP tool (`mcp__sequentialthinking__sequentialthinking`) to think through the problem step by step before presenting conclusions
```

- [ ] **Step 2: Add sequential thinking MCP to Spec Self-Review**

In `skills/brainstorming/SKILL.md`, in the "Spec Self-Review" section, add a new numbered item after item 4 (Ambiguity check):

```markdown
5. **Complex reasoning check:** For specs with intricate logic, multi-system interactions, or non-obvious trade-offs, use the sequential thinking MCP tool (`mcp__sequentialthinking__sequentialthinking`) to systematically verify consistency and completeness.
```

---

## Task 2: Modify Writing Plans — Remove Build/Test/Git Commands

**Files:**
- Modify: `skills/writing-plans/SKILL.md:38-45` (Bite-Sized Task Granularity)
- Modify: `skills/writing-plans/SKILL.md:65-104` (Task Structure example)
- Modify: `skills/writing-plans/SKILL.md:115-119` (Remember section)

- [ ] **Step 1: Simplify Bite-Sized Task Granularity section**

Replace the content of the "Bite-Sized Task Granularity" section (lines 38-45) with:

```markdown
## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Implement the minimal code to make the test pass" - step
- "Refactor if needed" - step
```

- [ ] **Step 2: Simplify the Task Structure template**

Replace the Task Structure section (lines 65-104) with:

`````markdown
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
`````

- [ ] **Step 3: Update Remember section**

Replace the Remember section (lines 115-119) with:

```markdown
## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- DRY, YAGNI, TDD
```

---

## Task 3: Add Sequential Thinking MCP to Writing Plans

**Files:**
- Modify: `skills/writing-plans/SKILL.md` (after Overview section, before Scope Check)

- [ ] **Step 1: Add sequential thinking MCP guidance**

Insert a new section after the Overview (after line 12, before "## Scope Check"):

```markdown
## Sequential Thinking

For complex decomposition decisions, architectural trade-offs, or multi-step reasoning about file structure and task ordering, use the sequential thinking MCP tool (`mcp__sequentialthinking__sequentialthinking`) to think through the problem step by step before committing to a plan structure.
```

---

## Task 4: Add Philosophy of Software Design Principles to Writing Plans

**Files:**
- Modify: `skills/writing-plans/SKILL.md` (insert new section after File Structure, before Bite-Sized Task Granularity)

- [ ] **Step 1: Add Design Principles section**

Insert a new section after "File Structure" and before "Bite-Sized Task Granularity":

```markdown
## Design Principles (Philosophy of Software Design)

All code in plan tasks MUST follow these principles. Code blocks that violate them are plan failures.

### Comment Best Practices
- Comments describe things that aren't obvious from the code — the *why*, not the *what*.
- Every module/class gets a high-level comment explaining its purpose and how it fits into the system.
- Interface comments describe what the method does, not how it does it.
- Never write "add comments later" — doc comments are part of the code, not a follow-up step.

### XML Doc Comments for Public Members
All publicly visible members (classes, methods, properties, enums) must have language-appropriate doc comments inline in plan code blocks:
- **C#:** `/// <summary>` XML doc comments
- **TypeScript/JavaScript:** JSDoc (`/** */`)
- **Python:** docstrings (`"""..."""`)

### Deep Modules
- Modules should provide simple interfaces that hide complex implementations.
- A deep module does a lot of work behind a small API surface.
- When planning file structure and interfaces, favor fewer, more powerful abstractions over many shallow ones.
- If a module's interface is as complex as its implementation, the module is too shallow — redesign it.

### Define Errors Out of Existence
- Design APIs so error conditions can't arise in the first place.
- Prefer designs where the caller can't pass invalid input over designs that validate and throw.
- Use typed enums instead of arbitrary strings. Use builder patterns that enforce required fields at compile time.
- If you find yourself writing extensive input validation, reconsider the API shape.
```

---

## Task 5: Rebrand Plugin Identity

**Files:**
- Modify: `.claude-plugin/plugin.json`
- Modify: `.claude-plugin/marketplace.json`
- Modify: `package.json`

- [ ] **Step 1: Update `.claude-plugin/plugin.json`**

Replace the entire contents with:

```json
{
  "name": "superpowers-winprn",
  "description": "Customized skills library for Claude Code: TDD, debugging, collaboration patterns, Philosophy of Software Design principles, and sequential thinking",
  "version": "1.0.0",
  "author": {
    "name": "Man Ly",
    "email": "man.ly@orangelogic.com"
  },
  "homepage": "https://github.com/winprn/superpowers-winprn",
  "repository": "https://github.com/winprn/superpowers-winprn",
  "license": "MIT",
  "keywords": [
    "skills",
    "tdd",
    "debugging",
    "collaboration",
    "best-practices",
    "workflows",
    "philosophy-of-software-design",
    "sequential-thinking"
  ]
}
```

- [ ] **Step 2: Update `.claude-plugin/marketplace.json`**

Replace the entire contents with:

```json
{
  "name": "superpowers-winprn",
  "description": "Customized skills library with Philosophy of Software Design principles and sequential thinking",
  "owner": {
    "name": "Man Ly",
    "email": "man.ly@orangelogic.com"
  },
  "plugins": [
    {
      "name": "superpowers-winprn",
      "description": "Customized skills library for Claude Code: TDD, debugging, collaboration patterns, Philosophy of Software Design principles, and sequential thinking",
      "version": "1.0.0",
      "source": "./",
      "author": {
        "name": "Man Ly",
        "email": "man.ly@orangelogic.com"
      }
    }
  ]
}
```

- [ ] **Step 3: Update `package.json`**

Replace the entire contents with:

```json
{
  "name": "superpowers-winprn",
  "version": "1.0.0",
  "type": "module",
  "main": ".opencode/plugins/superpowers.js"
}
```
