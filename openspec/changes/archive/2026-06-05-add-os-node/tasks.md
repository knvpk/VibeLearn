## 1. OS Node Body Schema

- [x] 1.1 Create `assets/schemas/content/os.json` with `## Overview` (string, write-once) and `## Notes` (array, append-only) sections

## 2. OS Node Frontmatter Schema

- [x] 2.1 Create `assets/schemas/os.json` with required fields: `id`, `collection` (const `"os"`), `name`, `family` (enum: linux|unix|windows|bsd|android|other), `form_factor` (enum: desktop|server|mobile|embedded)
- [x] 2.2 Add optional nullable fields to `os.json`: `version`, `arch` (string array), `kernel`, `package_manager`, `eol` (ISO date or null), `lts` (boolean or null)
- [x] 2.3 Add relation fields to `os.json`: `tools` (wikilink array, default `[]`), `tags` (string array, default `[]`)
- [x] 2.4 Add `x-body-sections` ref to `content/os.json` and include a complete `examples` entry and `x-file-example` string in `os.json`

## 3. SKILL.md Registration

- [x] 3.1 Add `schemas/os.json` and `schemas/content/os.json` to hardcoded schema path lists in `SKILL.md`
- [x] 3.2 Add `os/<id>.md` node directory entry to `SKILL.md`

## 4. Tool Schema Migration

- [x] 4.1 Remove `platform` property from `assets/schemas/tool.json`
- [x] 4.2 Add `supported_platforms` property to `assets/schemas/tool.json` — nullable array of wikilink strings
- [x] 4.3 Update the `examples` array and `x-file-example` in `tool.json` to use `supported_platforms` instead of `platform`
