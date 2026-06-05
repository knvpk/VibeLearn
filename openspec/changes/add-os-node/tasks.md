## 1. OS Node Body Schema

- [ ] 1.1 Create `assets/schemas/content/os.json` with `## Overview` (string, write-once) and `## Notes` (array, append-only) sections

## 2. OS Node Frontmatter Schema

- [ ] 2.1 Create `assets/schemas/os.json` with required fields: `id`, `collection` (const `"os"`), `name`, `family` (enum: linux|unix|windows|bsd|android|other), `form_factor` (enum: desktop|server|mobile|embedded)
- [ ] 2.2 Add optional nullable fields to `os.json`: `version`, `arch` (string array), `kernel`, `package_manager`, `eol` (ISO date or null), `lts` (boolean or null)
- [ ] 2.3 Add relation fields to `os.json`: `tools` (wikilink array, default `[]`), `tags` (string array, default `[]`)
- [ ] 2.4 Add `x-body-sections` ref to `content/os.json` and include a complete `examples` entry and `x-file-example` string in `os.json`

## 3. Tool Schema Migration

- [ ] 3.1 Remove `platform` property from `assets/schemas/tool.json`
- [ ] 3.2 Add `supported_platforms` property to `assets/schemas/tool.json` — nullable array of wikilink strings
- [ ] 3.3 Update the `examples` array and `x-file-example` in `tool.json` to use `supported_platforms` instead of `platform`
