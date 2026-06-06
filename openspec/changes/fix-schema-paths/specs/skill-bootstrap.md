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

Claude resolves these relative to cwd (the user's project root). There is no `schemas/` folder at the project root after a `npx skills add` install — schemas are at `<install-dir>/assets/schemas/`, which varies by install scope.

## After

The startup block probes for `SKILL_DIR` by checking two candidate paths in order:

```
1. .agents/skills/vibe_learn          (project-level install, relative to project root)
2. ~/.agents/skills/vibe_learn        (global install, relative to home directory)
```

The first candidate whose `assets/schemas/concept.json` is readable becomes `SKILL_DIR`.

If neither resolves, emit:
```
Schema files not found. Re-install with:
  project: npx skills add knvpk/VibeLearn
  global:  npx skills add knvpk/VibeLearn --global
```
and halt.

All 27 schema path references are updated to `{SKILL_DIR}/assets/schemas/<type>.json`.

## Constraints

- The probe runs once at startup before any schema read — not on every read.
- Probe order is project-first: a project-level install takes precedence over a global one.
- `SKILL_DIR` is set to the resolved path and reused for all 27 references.
- No other files change.
- The 27 path replacements are purely textual — no logic changes beyond the probe.
