## Context

Built-in schemas live in `{SKILL_DIR}/assets/schemas/` and are hardcoded in SKILL.md. The skill already has a unified node-handling pipeline (create, ingest, link, lint, index). The goal is to load user schemas into that same pipeline at startup, so custom types get identical treatment with no special-casing.

## Goals / Non-Goals

**Goals:**
- Load `{wiki.root}.schemas/*.json` at startup and merge into the known-types registry.
- Enforce `id` and `collection` as mandatory fields on every custom schema.
- Provide `generate schema <type>` to scaffold valid schema files interactively.
- Apply create, ingest, link, lint, and index uniformly to custom types.

**Non-Goals:**
- Validating schema field types beyond `required` vs. optional (no JSON Schema deep validation).
- Allowing custom schemas to override built-in types.
- Hot-reloading schemas mid-session without restart.

## Schema Format

Every custom schema file must conform to this shape:

```json
{
  "$schema": "vibe_learn/schema/v1",
  "type": "<singular-name>",
  "collection": "<folder-name>",
  "fields": {
    "id":    { "type": "string", "required": true },
    "title": { "type": "string", "required": true },
    "<field>": { "type": "string|array|enum", "required": false }
  },
  "content": {
    "sections": ["<section1>", "<section2>"]
  }
}
```

- `type` — singular noun used in commands (`create paper X`, `define paper`)
- `collection` — folder name under `wiki.root`; user controls pluralisation
- `fields.id` — always required; used as filename and cross-reference anchor
- `content.sections` — ordered list of H2 sections written into each node file

## Startup Flow (updated)

```
read vibe_learn.config.yaml
  ↓
locate SKILL_DIR (existing probe logic, unchanged)
  ↓
load built-in schemas from {SKILL_DIR}/assets/schemas/
  ↓
resolve additional_schemas_path:
  if wiki.additional_schemas set → {wiki.root}{wiki.additional_schemas}
  else                           → {wiki.root}.schemas/
  ↓
if additional_schemas_path exists:
  for each *.json in path:
    - validate: "id" in fields (required: true) AND "collection" present
    - if invalid: warn user, skip file, continue
    - register type → collection mapping
    - merge into known-types registry
  ↓
proceed — built-in + custom types treated identically
```

## Decisions

### D1 — `.schemas/` default, relative to `wiki.root`

**Decision**: Default path is `{wiki.root}.schemas/` (dot-prefixed, hidden from casual browsing). Overridable via `wiki.additional_schemas` in config.

**Rationale**: Co-locates schema definitions with wiki content for easy versioning. Dot-prefix keeps it out of sight alongside `.state/`. Users who want it elsewhere can set `wiki.additional_schemas: custom_schemas/`.

**Alternative rejected**: Path relative to project root — splits wiki-related files across two directories, complicating portability.

### D2 — Missing `.schemas/` folder is silent no-op

**Decision**: If `{wiki.root}.schemas/` doesn't exist, skip without warning.

**Rationale**: Existing users have no `.schemas/` folder. Showing a warning on every session start for a missing optional feature is noise.

### D3 — Invalid schema files warn and skip, not halt

**Decision**: A schema file missing `id` field or `collection` key logs a warning (`Custom schema <file> skipped: missing required "id" field`) and continues loading the rest.

**Rationale**: One malformed file should not block the entire session. User can fix and restart.

### D4 — Custom types cannot shadow built-in types

**Decision**: If a custom schema `type` matches a built-in type name, warn and skip the custom file.

**Rationale**: Shadowing built-in types would silently change behaviour in unpredictable ways.

### D5 — `generate schema` writes to `.schemas/`, creates folder if absent

**Decision**: The command always writes to `{additional_schemas_path}/<type>.json`, creating the folder if it doesn't exist yet.

**Rationale**: User shouldn't need to manually mkdir before running the command.

## Node File Convention for Custom Types

Custom type nodes follow the same convention as built-in nodes:

```
{wiki.root}{collection}/<id>.md
```

Frontmatter mirrors schema fields. Content sections come from `content.sections`. Filename is `<id>.md`.

## `generate schema` Command Flow

```
User: generate schema paper

Skill prompts:
  1. Collection folder name? (e.g. "papers")
  2. Required fields beyond id? (comma-separated names)
  3. Optional fields? (comma-separated names, or none)
  4. Content sections for each node? (comma-separated, e.g. "summary,key_findings,connections")

Skill writes: {additional_schemas_path}/paper.json
Skill confirms: Schema saved. Create your first paper with: create paper <id>
```

## Help Text Addition

The `help` command output gets a new **Customization** section:

```
### Customization
  generate schema <type>   → scaffold a new node type schema in {wiki.root}.schemas/
```
