## Why

`SKILL.md` hardcodes schema paths as `schemas/<type>.json` — relative to the **project root** of whatever project the skill is installed in. But `npx skills add knvpk/VibeLearn` installs the entire repo into `.agents/skills/vibe_learn/` (symlinked as `.claude/skills/vibe_learn/`), so the schemas live at `.claude/skills/vibe_learn/assets/schemas/` — not at `<project-root>/schemas/`.

Result: every `Read schemas/concept.json` call in the skill fails in any project that installs via `npx skills`.

## What Changes

- **`SKILL.md`** — introduce a single `SKILL_DIR` constant at startup and replace all 27 bare `schemas/` path references with `{SKILL_DIR}/assets/schemas/`.

## Capabilities

### Modified Capabilities
- `skill-bootstrap`: Add `SKILL_DIR` derivation to the startup block so all subsequent path references resolve correctly regardless of where the user's project is.

### New Capabilities
None.

## Impact

- `SKILL.md` only — no schema files, no config, no wiki content touched.
- After this change, `npx skills add` installs work out of the box; no manual `schemas/` copy step needed.
- Projects that manually copied `schemas/` to their project root will see no change in behaviour (the new path simply resolves to the installed skill dir instead).
