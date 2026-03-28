# Superpowers-winprn Fork Design

## Summary

Fork the superpowers plugin with targeted modifications to match personal workflow preferences, then publish to Claude marketplace under the name `superpowers-winprn`.

**Author:** Man Ly (man.ly@orangelogic.com)

## Changes

### 1. Sequential Thinking MCP Integration

**Affected skills:** brainstorming, writing-plans, spec review (brainstorming spec self-review)

Add guidance to use the sequential thinking MCP tool (`mcp__sequentialthinking__sequentialthinking`) when facing complex reasoning during design and planning phases.

**Placement:**
- **brainstorming/SKILL.md** — Add to "Key Principles" section: guidance to use sequential thinking MCP for complex design decisions, trade-off analysis, or multi-step reasoning before presenting conclusions.
- **writing-plans/SKILL.md** — Add to the overview or a new principles section: guidance to use sequential thinking MCP when decomposing complex tasks, determining file structure, or evaluating architectural decisions.
- **brainstorming/SKILL.md (spec self-review)** — Add guidance to consider sequential thinking MCP when checking for contradictions, ambiguity, or completeness in the spec.

**Tone:** "Consider using" — not mandatory for every decision, but strongly recommended when complexity warrants it.

### 2. Writing Plans — Remove Build/Test/Git Commands

**Affected skill:** writing-plans

Remove all build commands, test run commands, and git commands from the plan template. The engineer handles toolchain invocation themselves — the project is large and running these inline is impractical.

**Specific changes:**
- **Task template:** Reduce to three steps per behavior:
  1. Write the failing test (with code block)
  2. Write minimal implementation (with code block)
  3. Refactor if needed
- **"Bite-Sized Task Granularity" section:** Simplify the example to only show write test → implement → refactor. Remove "Run it to make sure it fails", "Run the tests and make sure they pass", and "Commit" as separate steps.
- **Task Structure example:** Remove `Run:` steps (steps 2, 4) and the `git add`/`git commit` step (step 5). Renumber remaining steps.
- **"Remember" section:** Remove "Exact commands with expected output" bullet.

### 3. Philosophy of Software Design Principles

**Affected skill:** writing-plans

Add a new "Design Principles (Philosophy of Software Design)" section to writing-plans/SKILL.md. All code blocks in plan tasks must follow these principles:

#### Comment Best Practices
- Comments describe things that aren't obvious from the code — the *why*, not the *what*.
- Every module/class gets a high-level comment explaining its purpose and how it fits into the system.
- Interface comments describe what the method does, not how it does it.

#### XML Doc Comments for Public Members
- All publicly visible members (classes, methods, properties, enums) must have language-appropriate doc comments:
  - C#: `/// <summary>` XML doc comments
  - TypeScript/JavaScript: JSDoc (`/** */`)
  - Python: docstrings
- Plan code blocks must include these doc comments inline — never as a "add docs later" step.

#### Deep Modules
- Modules should provide simple interfaces that hide complex implementations.
- A deep module does a lot of work behind a small API surface.
- When planning file structure and interfaces, favor fewer, more powerful abstractions over many shallow ones.

#### Define Errors Out of Existence
- Design APIs so error conditions can't arise in the first place.
- Prefer designs where the caller can't pass invalid input over designs that validate and throw.
- Example: use typed enums instead of arbitrary strings, use builder patterns that enforce required fields at compile time.

### 4. Plugin Rebranding

**Affected files:** `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `package.json`

Update plugin identity for marketplace publishing:

- **Plugin name:** `superpowers-winprn`
- **Author:** Man Ly (man.ly@orangelogic.com)
- **Description:** Keep the existing description or lightly adapt it.
- **Homepage/repository:** Update to the new fork's GitHub URL once created.
- **Version:** Reset to `1.0.0` to indicate this is a distinct fork.

## Files Modified

| File | Change |
|------|--------|
| `skills/brainstorming/SKILL.md` | Add sequential thinking MCP guidance to Key Principles and Spec Self-Review |
| `skills/writing-plans/SKILL.md` | Add sequential thinking MCP, add PoSD Design Principles section, remove build/test/git commands from template |
| `.claude-plugin/plugin.json` | Rebrand name, author, version |
| `.claude-plugin/marketplace.json` | Rebrand marketplace name, owner, plugin entry |
| `package.json` | Rebrand name, author, version |

## Out of Scope

- No changes to TDD skill content (TDD methodology is already well-covered; the writing-plans skill will reference it).
- No changes to other skills (debugging, code review, etc.).
- No structural changes to how skills are organized or loaded.
- No changes to hooks, agents, or the brainstorm server.
