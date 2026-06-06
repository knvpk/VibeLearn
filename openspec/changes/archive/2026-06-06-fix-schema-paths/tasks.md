## 1. Add SKILL_DIR probe and startup guard

- [x] 1.1 In `SKILL.md`, locate the startup block (where config is read and state files are derived). Add before the first schema read:
  ```
  Locate SKILL_DIR at startup by probing in order:
    1. If .agents/skills/vibe_learn/assets/schemas/concept.json is readable
       → SKILL_DIR = .agents/skills/vibe_learn
    2. Else if ~/.agents/skills/vibe_learn/assets/schemas/concept.json is readable
       → SKILL_DIR = ~/.agents/skills/vibe_learn
    3. Else → print:
         Schema files not found. Re-install with:
           project: npx skills add knvpk/VibeLearn
           global:  npx skills add knvpk/VibeLearn --global
       and stop.
  ```

## 2. Replace all bare schema path references

- [x] 2.1 Replace all 48 occurrences of `schemas/` in `SKILL.md` with `{SKILL_DIR}/assets/schemas/`
  - Verify: `grep -n 'schemas/' SKILL.md | grep -v '{SKILL_DIR}/assets/schemas/'` returns only the 2 probe sentinel lines (74–75) which are correct
  - Verify: `grep -c '{SKILL_DIR}/assets/schemas/' SKILL.md` returns 50

## 3. Verify

- [x] 3.1 All bare `schemas/` references replaced; only the 2 probe sentinel paths on lines 74–75 remain outside the `{SKILL_DIR}` prefix
- [x] 3.2 `{SKILL_DIR}/assets/schemas/` appears 50 times in SKILL.md
- [x] 3.3 Startup block confirmed: probe on lines 73–76, ordered project-level first, global second, error third, before any schema read
