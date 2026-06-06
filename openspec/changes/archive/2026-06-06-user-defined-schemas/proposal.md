## Why

The skill ships with a fixed set of node types (concept, source, author, tool, workflow, term, idea, collection, language, company, os). Users building specialized wikis — research databases, legal knowledge bases, reading lists — need node types the skill doesn't provide. Today they have no way to extend the schema set without modifying the skill itself.

## What Changes

- **Modified** `assets/vibe_learn.config.template.yaml` — adds an optional `wiki.additional_schemas` key (defaults to `.schemas/` relative to `wiki.root`).
- **Modified** `SKILL.md` — Startup section loads custom schemas from `{wiki.root}.schemas/` (or configured path) after built-in schemas; all node-handling logic (create, ingest, link, lint, index) applies uniformly to built-in and custom types.
- **New** `generate schema <type>` command — interactive wizard that writes a validated schema file to `{wiki.root}.schemas/<type>.json`.

No new built-in schema files. No breaking changes to existing configs.

## Capabilities

### New Capabilities
- `user-defined-schemas`: Load and enforce user-provided JSON schemas from `{wiki.root}.schemas/`, treating custom types as first-class wiki node types.

### Modified Capabilities
- `config`: `wiki.additional_schemas` optional key added to the config template; defaults to `.schemas/`.
- `create`: Handles `create <custom-type> <id>` for any registered custom type.
- `ingest`: Maps ingested content to custom types when their schema fields match.
- `lint`: Health-check scans custom type collections for orphans, stubs, and missing required fields.
- `index`: `index.md` and Obsidian `.base` files include custom type collections.

## Impact

- `assets/vibe_learn.config.template.yaml` — additive; existing configs without `wiki.additional_schemas` use the `.schemas/` default silently (no `.schemas/` folder = no custom types, no error).
- `SKILL.md` — targeted edits to Startup, Create, Ingest, Lint, and Index sections.
- Users with custom schemas gain full first-class node support. Users without `.schemas/` see zero behaviour change.
