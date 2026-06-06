## Context

`SKILL.md` is a plain-text instruction file for Claude Code. It has no runtime variable system — but it can define a constant in prose that Claude treats as a substitution variable throughout the document. The goal is to fix 27 hardcoded `schemas/` paths while avoiding 27-line churn by centralising the install path in one place.

## Goals / Non-Goals

**Goals:**
- Schema reads work immediately after `npx skills add knvpk/VibeLearn` with zero manual setup.
- Single point of truth for the install path — one edit if the path ever changes.
- Clear error message when schemas are missing.

**Non-Goals:**
- Supporting multiple install locations or dynamic path resolution at runtime.
- Changing any schema file content.
- Adding shell scripts or package.json scripts.

## Decisions

### D1 — Define `SKILL_DIR` as a prose constant in the startup block

**Decision**: Add this line to the startup block:

```
SKILL_DIR = .agents/skills/vibe_learn
```

Then write all schema paths as `{SKILL_DIR}/assets/schemas/<type>.json`.

**Rationale**: Claude follows prose instructions literally. A single declared constant at the top is equivalent to a variable — Claude will substitute it wherever it appears. This gives us one place to update the path without touching 27 call sites.

**Alternative rejected**: Replace all 27 occurrences with the literal full path (`.agents/skills/vibe_learn/assets/schemas/...`). Works, but creates 27 places to update if the install path changes, and makes the file visually noisy.

### D2 — Use `.agents/skills/vibe_learn` (the real install location)

**Decision**: The constant points to the real install at `.agents/skills/vibe_learn`, not the `.claude/` symlink.

**Rationale**: `.agents/skills/vibe_learn` is where `npx skills` actually writes the files. Using the real path avoids any symlink resolution ambiguity and is consistent with how `npx skills` manages the install directory.

### D3 — Guard on missing schemas in startup, not on every read

**Decision**: One readability check at startup:
```
If {SKILL_DIR}/assets/schemas/concept.json is not readable → emit error + halt
```

**Rationale**: Failing fast at startup with a clear message is better than failing silently on the first schema read mid-session. A single check on `concept.json` is sufficient — if the skill is installed, all schemas are present; if it isn't, none are.

## Risks / Trade-offs

- **`.agents/skills/vibe_learn` is still a hardcoded path** — if the user renames the skill or installs it differently, the path breaks. Acceptable: `npx skills` always uses the `name` field from `skill.json` (`vibe_learn`) as the directory name, so this is stable.
- **Symlink resolution** — Claude resolves file paths; symlinks are transparent to `Read`. No issue.
