## Context

Every wiki node schema has a `collection` field with a `const` value (`concept`, `source`, `tool`, etc.). Obsidian Bases queries this field to populate database views. The skill already writes these nodes; the missing piece is the `.base` sidecar files that tell Obsidian which views to render and which columns to show.

The feature is purely additive: `.base` files are static JSON, written once, never modified by the skill after creation.

## Goals / Non-Goals

**Goals:**
- Generate one `.base` file per node collection under `{wiki.root}bases/` the first time any node of that type is written.
- Gate generation behind `obsidian.enabled: true` in `vibe_learn.config.yaml`.
- Provide useful out-of-the-box views (table + board where a natural grouping field exists).

**Non-Goals:**
- Re-generating or updating `.base` files after initial creation — they are static once written.
- Validating that the user's vault is actually opened in Obsidian.
- Supporting Obsidian-specific wikilink syntax changes — wikilinks are already unconditional.
- Providing a command to regenerate bases on demand (out of scope; user can delete and re-trigger).

## Base File Format

Obsidian Bases files are JSON with a `filters` block and a `views` array:

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "<collection>" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "<Human Name> — All",
      "columns": ["name", "<field2>", "<field3>", "tags"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "<Human Name> — By <Field>",
      "groupBy": "<field>"
    }
  ]
}
```

## Decisions

### D1 — Lazy creation on node write, not on startup

**Decision**: After writing any node file, check if `{wiki.root}bases/<collection>.base` exists. If not, and `obsidian.enabled` is true, write it.

**Rationale**: Startup is already load-heavy (config, state files, schema reads). Generating 11 base files eagerly on every session start is wasteful if the user never writes a node. Lazy creation ties the base file to the moment the node type first exists — semantically correct and zero overhead for sessions that only teach without writing.

**Alternative rejected**: A one-time "setup obsidian" command. Requires user to remember to run it; lazy creation is invisible and automatic.

### D2 — Views per collection

Nodes with a natural low-cardinality grouping field get a table view + board view. Others get table only.

| Collection  | Table columns                        | Board groupBy  |
|-------------|--------------------------------------|----------------|
| concept     | name, difficulty, tags               | difficulty     |
| source      | title, type, author, date_ingested   | type           |
| tool        | name, category, maturity, docker     | category       |
| workflow    | name, status, difficulty             | status         |
| idea        | title, status, priority              | status         |
| os          | name, family, form_factor            | family         |
| author      | name, company, verdict               | (table only)   |
| term        | name, tags                           | (table only)   |
| collection  | title, catalog_type, date_ingested   | (table only)   |
| language    | name, execution                      | (table only)   |
| company     | name, founded, verdict               | (table only)   |

**Rationale**: Board views are useful when the group-by field has a stable, small set of values. `difficulty`, `type`, `category`, `status`, and `family` all have enum-constrained values. Fields like `name`, `verdict`, `execution`, or `founded` do not form natural swim lanes.

### D3 — Files stored at `{wiki.root}bases/<collection>.base`

**Decision**: Use the `bases/` subdirectory under `wiki.root`, not a top-level path.

**Rationale**: Keeps the wiki directory self-contained. Obsidian Bases discovers `.base` files anywhere in the vault, so the subdirectory has no functional downside and avoids polluting `wiki.root` directly.

### D4 — `obsidian.enabled` is opt-in (default false)

**Decision**: Treat a missing `obsidian:` key as `enabled: false`.

**Rationale**: Users not using Obsidian should not accumulate `.base` files. Opt-in avoids surprising non-Obsidian users with unknown file types in their wiki.

## Risks / Trade-offs

- **Obsidian Bases format may evolve** → `.base` files are written once; the user can delete and re-trigger if the format changes. Acceptable for a static feature.
- **Collection name collisions** → If a future collection is added with a name matching an existing base file, the existing file will not be regenerated. Mitigated by the non-overwrite policy — user deletes to force refresh.
