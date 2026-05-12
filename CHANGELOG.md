# Changelog

## [1.3.0] - 2026-05-12

### Changed

- **using-git-worktrees, executing-plans, subagent-driven-development, writing-plans**: Worktrees are now OPTIONAL. The default workflow uses a regular feature branch off main. Worktrees are reserved for cases where isolation is genuinely needed (parallel plans, untouched workspace) or when the user explicitly asks for one.

### Files touched

- `skills/using-git-worktrees/SKILL.md`
- `skills/executing-plans/SKILL.md`
- `skills/subagent-driven-development/SKILL.md`
- `skills/writing-plans/SKILL.md`

## [1.2.0] - 2026-05-12

### Added

- **decomposing-specs** (new skill): Forces explicit item enumeration before plan writing. Counters the default failure mode where agents read a multi-item spec, classify it as "one feature," and collapse it into a single plan file. Output is a decomposition table (id/slug/depends_on/touches) that seeds the writing-plans manifest directly.

### Changed

- **writing-plans**: Now requires `decomposing-specs` as a sub-skill before its "single file vs directory" decision. Step 1 of the directory flow consumes the decomposition table instead of re-enumerating items.

### Files touched

- `skills/decomposing-specs/SKILL.md` (new)
- `skills/writing-plans/SKILL.md`

## [1.1.0] - 2026-05-12

### Changed

- **writing-plans**: Multi-item specs now produce a **plan directory** (`docs/superpowers/plans/YYYY-MM-DD-<topic>/`) with a YAML manifest and per-item plan files. A controller dispatches plan-writer subagents in **topological waves** so independent items are planned in parallel. Adds cross-plan consistency review for type/signature drift. Single-item specs unchanged.
- **executing-plans**: Now detects plan directories and processes items in topological order based on the manifest's `depends_on` graph (sequential, since this skill assumes no subagents).
- **subagent-driven-development**: For plan directories, dispatches implementer subagents in **parallel waves** with a file-ownership guard that prevents concurrent work on overlapping `touches` paths. Per-item spec and code-quality reviewers now run in parallel. Final cross-cutting code review at the end.

### Added

- `skills/writing-plans/plan-writer-prompt.md`: subagent prompt template for per-item plan writing.

### Files touched

- `skills/writing-plans/SKILL.md`
- `skills/writing-plans/plan-writer-prompt.md` (new)
- `skills/executing-plans/SKILL.md`
- `skills/subagent-driven-development/SKILL.md`
- `docs/superpowers/specs/2026-05-12-parallel-plan-writing-design.md` (design spec)

## [5.0.5] - 2026-03-17

### Fixed

- **Brainstorm server ESM fix**: Renamed `server.js` → `server.cjs` so the brainstorming server starts correctly on Node.js 22+ where the root `package.json` `"type": "module"` caused `require()` to fail. ([PR #784](https://github.com/obra/superpowers/pull/784) by @sarbojitrana, fixes [#774](https://github.com/obra/superpowers/issues/774), [#780](https://github.com/obra/superpowers/issues/780), [#783](https://github.com/obra/superpowers/issues/783))
- **Brainstorm owner-PID on Windows**: Skip `BRAINSTORM_OWNER_PID` lifecycle monitoring on Windows/MSYS2 where the PID namespace is invisible to Node.js. Prevents the server from self-terminating after 60 seconds. The 30-minute idle timeout remains as the safety net. ([#770](https://github.com/obra/superpowers/issues/770), docs from [PR #768](https://github.com/obra/superpowers/pull/768) by @lucasyhzhu-debug)
- **stop-server.sh reliability**: Verify the server process actually died before reporting success. Waits up to 2 seconds for graceful shutdown, escalates to `SIGKILL`, and reports failure if the process survives. ([#723](https://github.com/obra/superpowers/issues/723))

### Changed

- **Execution handoff**: Restore user choice between subagent-driven-development and executing-plans after plan writing. Subagent-driven is recommended but no longer mandatory. (Reverts `5e51c3e`)
