## 1. Add SKILL_DIR constant and startup guard

- [ ] 1.1 In `SKILL.md`, locate the startup block (where config is read and state files are derived). Add immediately after the first `##` heading or before the first file-read instruction:
  ```
  SKILL_DIR = .claude/skills/vibe_learn
  ```
- [ ] 1.2 Add a guard below the constant: if `{SKILL_DIR}/assets/schemas/concept.json` is not readable, print `Schema files not found at {SKILL_DIR}/assets/schemas/. Re-install with: npx skills add knvpk/VibeLearn` and stop.

## 2. Replace all bare schema path references

- [ ] 2.1 Replace all 27 occurrences of `schemas/` in `SKILL.md` with `{SKILL_DIR}/assets/schemas/`
  - Verify: `grep -c 'schemas/' SKILL.md` returns 0 after replacement
  - Verify: `grep -c '{SKILL_DIR}/assets/schemas/' SKILL.md` returns 27

## 3. Verify

- [ ] 3.1 Run `grep 'schemas/' SKILL.md` — must return no matches (all replaced)
- [ ] 3.2 Run `grep '{SKILL_DIR}/assets/schemas/' SKILL.md` — must return exactly 27 matches
- [ ] 3.3 Read the startup block section of `SKILL.md` and confirm `SKILL_DIR` constant and guard are present and correctly placed before the first schema read
