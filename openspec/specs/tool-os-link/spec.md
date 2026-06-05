## Purpose

Defines the bidirectional linking between tool nodes and OS nodes via wikilinks in schema properties.

## Requirements

### Requirement: tool.supported_platforms replaces tool.platform
`assets/schemas/tool.json` SHALL remove the `platform` property and add `supported_platforms` — a nullable array of `[[wikilink]]` strings referencing OS nodes.

Before: `platform` — array of enum strings `["linux", "macos", "windows", "web", "mobile", "wasm", "any"]`.
After: `supported_platforms` — array of wikilink strings (e.g. `["[[ubuntu_22_04]]", "[[macos_ventura]]"]`), or `null`.

#### Scenario: tool with supported_platforms wikilinks passes validation
- **WHEN** a tool frontmatter contains `supported_platforms: ["[[ubuntu_22_04]]", "[[windows_11]]"]`
- **THEN** the schema SHALL validate successfully

#### Scenario: tool with supported_platforms null passes validation
- **WHEN** a tool frontmatter contains `supported_platforms: null`
- **THEN** the schema SHALL validate successfully

#### Scenario: tool with old platform field fails validation
- **WHEN** a tool frontmatter contains `platform: ["linux"]` (old field)
- **THEN** the schema SHALL report a validation error due to `additionalProperties: false`

#### Scenario: tool schema example is updated
- **WHEN** the `examples` array and `x-file-example` in `tool.json` are read
- **THEN** they SHALL reference `supported_platforms` and SHALL NOT reference `platform`

### Requirement: os.tools back-links to tool nodes
`assets/schemas/os.json` SHALL include a `tools` property — an array of `[[wikilink]]` strings referencing tool nodes, defaulting to an empty array.

#### Scenario: OS node with tools back-links passes validation
- **WHEN** an OS frontmatter contains `tools: ["[[docker]]", "[[pytorch]]"]`
- **THEN** the schema SHALL validate successfully

#### Scenario: OS node with empty tools array passes validation
- **WHEN** an OS frontmatter contains `tools: []`
- **THEN** the schema SHALL validate successfully
