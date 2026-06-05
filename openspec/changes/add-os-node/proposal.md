## Why

The `tool` schema uses a flat `platform` enum (`linux`, `macos`, `windows`) that cannot represent specific OS versions, architectures, or lifecycle metadata. This blocks meaningful graph queries like "which tools support ARM64 Ubuntu?" or "which tools still support an EOL OS?". Replacing it with first-class `os` nodes and a `supported_platforms` wikilink array enables precise, navigable OS–tool relationships.

## What Changes

- **New** `assets/schemas/os.json` — frontmatter schema for OS nodes with `family`, `form_factor`, `version`, `arch`, `kernel`, `package_manager`, `eol`, `lts`, `desktop`, and back-link `tools` array.
- **New** `assets/schemas/content/os.json` — body sections schema (`## Overview`, `## Notes`) for OS markdown files.
- **BREAKING** `assets/schemas/tool.json` — remove `platform` (enum array), add `supported_platforms` (array of `[[wikilink]]` references to `os` nodes).

## Capabilities

### New Capabilities
- `os-node`: Rich OS node schema with kernel family, form factor, version, architecture, EOL, and bidirectional tool links.
- `tool-os-link`: `supported_platforms` property on `tool` nodes — wikilink array replacing the old `platform` enum.

### Modified Capabilities

## Impact

- `assets/schemas/tool.json` — one property removed (`platform`), one added (`supported_platforms`).
- `assets/schemas/os.json` — new file.
- `assets/schemas/content/os.json` — new file.
- Any existing tool markdown files using `platform:` frontmatter will need migration to `supported_platforms:`.
