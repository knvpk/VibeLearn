## 1. Config Template

- [ ] 1.1 Add `wiki.additional_schemas` key to `assets/vibe_learn.config.template.yaml` as a commented-out optional key with a note that it defaults to `.schemas/` relative to `wiki.root`
  - Done condition: file contains `additional_schemas` key (commented out) with a description of the default

## 2. SKILL.md — Startup: Load Custom Schemas

- [ ] 2.1 After the built-in schema loading block in the **Startup** section, add a step that resolves `additional_schemas_path` from `wiki.additional_schemas` config key (default: `{wiki.root}.schemas/`)
  - Done condition: `SKILL.md` Startup section references `additional_schemas_path` resolution with the default fallback
- [ ] 2.2 Add a step that globs `{additional_schemas_path}/*.json`, validates each file for `id` field and `collection` key, warns-and-skips on invalid, and merges valid schemas into the known-types registry
  - Done condition: `SKILL.md` describes the glob + validate + merge logic and the warn-and-skip behaviour for missing `id`/`collection`
- [ ] 2.3 Add a guard: if a custom schema `type` matches a built-in type name, warn and skip that file
  - Done condition: `SKILL.md` mentions the shadowing guard by name

## 3. SKILL.md — `generate schema` Command

- [ ] 3.1 Add `generate schema <type>` to the **Help** command reference under a new `Customization` section
  - Done condition: `help` output block in `SKILL.md` includes `generate schema <type>` with a one-line description
- [ ] 3.2 Add the `generate schema` command handler: interactive four-step prompt (collection name, required fields, optional fields, content sections), writes `{additional_schemas_path}/<type>.json` creating the folder if absent, confirms with next-step hint
  - Done condition: `SKILL.md` contains the `generate schema` handler with all four prompts and the file-write step

## 4. SKILL.md — Create

- [ ] 4.1 Confirm the `create <type> <id>` handler already dispatches on the known-types registry. If it references a hardcoded list of types, update it to use the registry so custom types are handled automatically
  - Done condition: `SKILL.md` create handler does not enumerate built-in type names; it resolves from the registry

## 5. SKILL.md — Ingest

- [ ] 5.1 In the **Ingest** section, add a step: after built-in type detection, check custom types in the registry by matching ingested content fields against schema `fields` definitions; if a match is found above a confidence threshold, propose mapping to that custom type
  - Done condition: `SKILL.md` ingest section references custom-type detection from the registry

## 6. SKILL.md — Lint

- [ ] 6.1 In the **Lint / Health Check** section, add a step that iterates over all registered custom types, checks `{wiki.root}{collection}/` for orphaned files, stubs (nodes with no content sections populated), and frontmatter missing required fields, and reports findings in the same format as built-in lint output
  - Done condition: `SKILL.md` lint section mentions custom type collections and the three checks (orphans, stubs, missing required fields)

## 7. SKILL.md — Index & Obsidian Bases

- [ ] 7.1 In the **Index** section, ensure `index.md` generation iterates over all registered types (built-in + custom) rather than a hardcoded list
  - Done condition: `SKILL.md` index section does not enumerate built-in collection names; it iterates the registry
- [ ] 7.2 In the **Obsidian Bases** section (on-node-write lazy creation), confirm the base-file generation uses the registry so custom types automatically get a `.base` file when `obsidian.enabled` is true
  - Done condition: `SKILL.md` obsidian bases logic references the registry, not a hardcoded collection list
