## 1. Add SKILL_DIR probe and startup guard

- [ ] 1.1 In `SKILL.md`, locate the startup block (where config is read and state files are derived). Add before the first schema read:
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

- [ ] 2.1 Replace all 27 occurrences of `schemas/` in `SKILL.md` with `{SKILL_DIR}/assets/schemas/`
  - Verify: `grep -c 'schemas/' SKILL.md` returns 0 after replacement
  - Verify: `grep -c '{SKILL_DIR}/assets/schemas/' SKILL.md` returns 27

## 3. Verify

- [ ] 3.1 Run `grep 'schemas/' SKILL.md` — must return no matches (all replaced)
- [ ] 3.2 Run `grep '{SKILL_DIR}/assets/schemas/' SKILL.md` — must return exactly 27 matches
- [ ] 3.3 Read the startup block section of `SKILL.md` and confirm the probe is present and ordered correctly (project-level first, global second, error third) before the first schema read
