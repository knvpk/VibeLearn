## 1. Config Template

- [ ] 1.1 Add `obsidian` block to `assets/vibe_learn.config.template.yaml` with `enabled: false` and a comment explaining it gates Obsidian Bases generation
  - Done condition: file contains `obsidian:\n  enabled: false` with a descriptive comment above it

## 2. SKILL.md — Config Parsing

- [ ] 2.1 In the **Startup** section of `SKILL.md`, add `obsidian.enabled` to the list of config keys parsed from `vibe_learn.config.yaml`; note that a missing key is treated as `false`
  - Done condition: `SKILL.md` Startup section mentions `obsidian.enabled` and the default-false fallback

## 3. SKILL.md — On-Node-Write Base Generation

- [ ] 3.1 In `SKILL.md`, after every node write (concepts, sources, tools, workflows, authors, terms, ideas, collections, languages, companies, os), add a step: if `obsidian.enabled` is true, check if `{wiki.root}bases/<collection>.base` exists; if not, create it
  - Done condition: `SKILL.md` contains the lazy-base-creation rule referencing the `bases/` path
- [ ] 3.2 In the same section, specify the exact JSON to write for each collection's `.base` file, per the view table in design.md D2 (table columns and board groupBy per collection)
  - Done condition: `SKILL.md` either embeds the full JSON for all 11 collections, or references a new file under `references/` that contains it

## 4. Reference Doc (if externalised from SKILL.md)

- [ ] 4.1 If the base file JSON is too verbose for inline inclusion in `SKILL.md`, create `references/obsidian_bases.md` containing the JSON spec for all 11 collections, and add a link from the SKILL.md on-node-write section
  - Done condition: either `references/obsidian_bases.md` exists and is linked from `SKILL.md`, OR the JSON is inlined directly in `SKILL.md` (one or the other — not both)
