# Spec: skill-bootstrap

## Affected File

`SKILL.md`

## Before

The startup block reads config and state files but has no concept of where the skill itself is installed. Schema paths are referenced as bare relative paths:

```
schemas/concept.json
schemas/content/concept.json
schemas/state/plan.json
... (27 total occurrences)
```

Claude resolves these relative to cwd (the user's project root). There is no `schemas/` folder at the project root after a `npx skills add` install — schemas are at `.claude/skills/vibe_learn/assets/schemas/`.

## After

The startup block derives `SKILL_DIR` once:

```
SKILL_DIR = .claude/skills/vibe_learn
```

All 27 schema path references are updated to `{SKILL_DIR}/assets/schemas/<type>.json`.

The startup section also gains a guard: if `{SKILL_DIR}/assets/schemas/concept.json` is not readable, emit a one-line error:
```
Schema files not found at {SKILL_DIR}/assets/schemas/. Re-install with: npx skills add knvpk/VibeLearn
```
and halt.

## Constraints

- `SKILL_DIR` is a fixed string constant in the skill text — it is NOT derived at runtime from the filesystem. The value is always `.claude/skills/vibe_learn`.
- The path uses `.claude/skills/vibe_learn` (the symlink), not `.agents/skills/vibe_learn` (the real location), because `.claude/` is the stable, agent-agnostic reference point.
- No other files change.
- The 27 replacements are purely textual — no logic changes.
