## Context

`SKILL.md` is a plain-text instruction file for Claude Code. It has no runtime variable system — but Claude can follow prose instructions to probe the filesystem at startup and derive a path. The goal is to fix 27 hardcoded `schemas/` paths while handling both project-level and global installs.

`npx skills add` installs to two possible locations depending on scope:
- **Project-level**: `.agents/skills/vibe_learn/` (relative to project root)
- **Global**: `~/.agents/skills/vibe_learn/` (relative to home directory)

A hardcoded constant works for one scope but breaks the other.

## Goals / Non-Goals

**Goals:**
- Schema reads work after both `npx skills add knvpk/VibeLearn` and `npx skills add knvpk/VibeLearn --global`.
- Single probe at startup sets `SKILL_DIR` once — all 27 references reuse it.
- Clear error message when schemas are missing at either location.

**Non-Goals:**
- Supporting custom install paths beyond what `npx skills` produces.
- Changing any schema file content.
- Adding shell scripts or package.json scripts.

## Decisions

### D1 — Probe at startup, not a hardcoded constant

**Decision**: Replace the fixed `SKILL_DIR = <path>` with a startup probe:

```
Check .agents/skills/vibe_learn/assets/schemas/concept.json    → project-level
Check ~/.agents/skills/vibe_learn/assets/schemas/concept.json  → global
Set SKILL_DIR to whichever resolves first; error if neither.
```

**Rationale**: A hardcoded path only works for one install scope. The probe adds one `if/else` in prose instructions; Claude handles this naturally. Project-level is checked first so it takes precedence over a global install in the same environment.

**Alternative rejected**: Hardcode `.agents/skills/vibe_learn`. Works for project installs only — global installs at `~/.agents/` would always fail.

### D2 — Use `.agents/` paths, not `.claude/` symlinks

**Decision**: Probe `.agents/skills/vibe_learn` and `~/.agents/skills/vibe_learn`, not their `.claude/` symlink equivalents.

**Rationale**: `.agents/` is the real install location `npx skills` writes to. `.claude/skills/vibe_learn` is a symlink that resolves to `../../.agents/skills/vibe_learn` — using the real path is simpler and avoids any symlink resolution ambiguity.

### D3 — Guard on missing schemas in startup, not on every read

**Decision**: One probe at startup checks `concept.json` as a sentinel. If it's not readable, emit a scoped error message and halt.

**Rationale**: Failing fast at startup with a clear message is better than a cryptic failure mid-session on the first schema read. Checking `concept.json` is sufficient — if the skill is installed, all schemas are present.

## Risks / Trade-offs

- **Custom install paths not supported** — if a user runs `npx skills add` with a non-default base directory, neither candidate will match. Acceptable: non-default paths are an edge case not covered by `skill.json`.
- **Project-level takes precedence** — if both a project install and a global install exist, the project one wins. This matches typical developer expectations (local overrides global).
