## Context

The schema layer in `assets/schemas/` defines frontmatter for all knowledge-graph node types. Currently `tool.json` has a `platform` property — a flat enum array — that provides no linkage into a richer OS model. There are no OS nodes in the graph today.

The change adds two new schema files (`os.json`, `content/os.json`) and modifies `tool.json` to replace `platform` with `supported_platforms`.

## Goals / Non-Goals

**Goals:**
- Introduce a first-class `os` node schema with rich metadata (family, form_factor, version, arch, kernel, eol, lts, package_manager).
- Replace `tool.platform` (enum) with `tool.supported_platforms` (wikilink array).
- Support bidirectional navigation: `tool → os` via `supported_platforms`, `os → tool` via `tools`.

**Non-Goals:**
- Migrating existing tool markdown files — that is a follow-on data task.
- Adding `os` to any ingest pipeline or UI layer.
- Validating wikilink targets at schema validation time.

## Decisions

### D1 — `family` models kernel lineage, `form_factor` models runtime context

**Decision**: Split OS classification into two orthogonal fields.

```
family:      linux | unix | windows | bsd | android | other
form_factor: desktop | server | mobile | embedded
```

**Rationale**: A single `family` field cannot cleanly represent both kernel lineage and deployment context. macOS is Unix (not its own family); Android runs the Linux kernel but is a distinct ecosystem. Separating the two axes allows accurate classification without ambiguity:

| OS           | family  | form_factor |
|--------------|---------|-------------|
| Ubuntu 22.04 | linux   | server      |
| macOS Ventura| unix    | desktop     |
| Android 14   | android | mobile      |
| iOS 17       | unix    | mobile      |
| FreeBSD 14   | bsd     | server      |
| Windows 11   | windows | desktop     |

**Alternative rejected**: A single `family` enum including `macos` and `mobile` — technically inaccurate for macOS (it is Unix) and overloads family with form-factor concerns.

### D2 — `supported_platforms` uses wikilinks, not IDs

**Decision**: `supported_platforms` is `string[]` with `[[wikilink]]` format, matching the pattern used by `alternatives`, `concepts`, and `tools` throughout the schema layer.

**Rationale**: Consistent with every other cross-node reference in the codebase. Obsidian-style apps resolve these natively.

### D3 — Bidirectional: `os.tools` mirrors `tool.supported_platforms`

**Decision**: `os.json` includes a `tools` array of wikilinks back to tool nodes, matching the `language.json` pattern where `language.tools` back-links to tools.

**Rationale**: Enables graph traversal from an OS node ("what tools support this OS?") without requiring a reverse index.

### D4 — `eol` is nullable ISO date, not a boolean

**Decision**: `eol: "2027-04-01" | null` rather than a boolean `is_eol`.

**Rationale**: An exact date is strictly more useful — it can answer "is it EOL?" and "when does it go EOL?". Null covers rolling-release distros (e.g. Arch Linux) and cases where EOL is unknown.

## Risks / Trade-offs

- **Wikilink targets not validated** → An `os` node referenced in `supported_platforms` may not exist. Mitigation: accepted at schema level; downstream tooling can enforce referential integrity.
- **`platform` removal is breaking** → Existing tool files with `platform:` frontmatter will fail schema validation after the change. Mitigation: documented in proposal; migration is a follow-on task outside this change's scope.
- **`family: android` may feel odd** → Android being its own family value rather than `linux` is a pragmatic choice. It is documented in D1 rationale and consistent with ecosystem-based classification.
