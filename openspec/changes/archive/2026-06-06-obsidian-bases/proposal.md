## Why

The wiki uses `collection` frontmatter on every node type — a field that is structurally identical to what Obsidian Bases queries against. Today, users who open their wiki vault in Obsidian get no database views: no way to see all tools by category, all concepts by difficulty, or all ideas by status without manually browsing folders. Creating `.base` files unlocks Obsidian's built-in Bases feature for the entire graph with zero ongoing maintenance.

This is gated behind `obsidian.enabled: true` in `vibe_learn.config.yaml` because users who don't use Obsidian should not accumulate `.base` files they'll never open.

## What Changes

- **New** `assets/vibe_learn.config.template.yaml` — adds an `obsidian` block with `enabled: false` (opt-in).
- **Modified** `SKILL.md` — Startup section reads `obsidian.enabled`; on-node-write section creates `{wiki.root}bases/<collection>.base` lazily if missing and `obsidian.enabled` is true.

No new schema files. No new state files. Bases are derived entirely from existing frontmatter.

## Capabilities

### New Capabilities
- `obsidian-bases`: Lazy generation of `.base` files for all node types, gated by `obsidian.enabled`.

### Modified Capabilities
- `config`: `obsidian.enabled` opt-in key added to the config template and documented in SKILL.md startup parsing.

## Impact

- `assets/vibe_learn.config.template.yaml` — additive; existing configs without `obsidian:` key behave as `enabled: false`.
- `SKILL.md` — two targeted edits: (1) startup config parsing adds `obsidian.enabled`; (2) on-node-write adds lazy base file check.
- Users with `obsidian.enabled: true` will see `{wiki.root}bases/` populated the first time each node type is written. No breakage for users without the key.
