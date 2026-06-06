# Spec: skill-bootstrap

## Affected File

`SKILL.md`

## Behaviour

The startup block probes for `SKILL_DIR` before any schema read, checking two candidate paths in order:

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

All schema path references in `SKILL.md` use `{SKILL_DIR}/assets/schemas/<type>.json`.

## Constraints

- The probe runs once at startup before any schema read — not on every read.
- Probe order is project-first: a project-level install takes precedence over a global one.
- `SKILL_DIR` is set to the resolved path and reused for all schema references.
