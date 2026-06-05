## ADDED Requirements

### Requirement: OS node frontmatter schema exists
The file `assets/schemas/os.json` SHALL define a JSON Schema (draft-07) for the YAML frontmatter of every OS node markdown file.

Required fields: `id`, `collection` (const `"os"`), `name`, `family`, `form_factor`.

Optional nullable fields: `version`, `arch`, `kernel`, `package_manager`, `eol`, `lts`, `desktop`.

Relation fields (default empty array): `tools`, `tags`.

#### Scenario: Valid minimal OS node passes validation
- **WHEN** a frontmatter object contains only `id`, `collection: "os"`, `name`, `family`, and `form_factor`
- **THEN** the schema SHALL validate successfully

#### Scenario: Missing required field fails validation
- **WHEN** a frontmatter object omits `family`
- **THEN** the schema SHALL report a validation error

#### Scenario: Invalid family value fails validation
- **WHEN** `family` is set to a value not in `["linux", "unix", "windows", "bsd", "android", "other"]`
- **THEN** the schema SHALL report a validation error

#### Scenario: Invalid form_factor value fails validation
- **WHEN** `form_factor` is set to a value not in `["desktop", "server", "mobile", "embedded"]`
- **THEN** the schema SHALL report a validation error

#### Scenario: EOL as ISO date passes validation
- **WHEN** `eol` is set to a string matching `YYYY-MM-DD` format
- **THEN** the schema SHALL validate successfully

#### Scenario: EOL as null passes validation
- **WHEN** `eol` is explicitly set to `null`
- **THEN** the schema SHALL validate successfully

#### Scenario: arch as array of strings passes validation
- **WHEN** `arch` is set to `["x86_64", "arm64"]`
- **THEN** the schema SHALL validate successfully

### Requirement: OS node body sections schema exists
The file `assets/schemas/content/os.json` SHALL define the expected markdown body sections for OS node files.

Sections: `## Overview` (string, write-once description), `## Notes` (array, append-only user observations).

#### Scenario: Schema file is present and valid JSON
- **WHEN** `assets/schemas/content/os.json` is read and parsed
- **THEN** it SHALL be valid JSON with `$schema`, `$id`, `title`, `type: "object"`, and a `properties` object containing at least `## Overview` and `## Notes`

### Requirement: OS node example is accurate
`assets/schemas/os.json` SHALL include an `examples` array and an `x-file-example` string demonstrating a complete, valid OS node.

#### Scenario: Example validates against the schema
- **WHEN** the first entry in the `examples` array is validated against the schema's own `properties`
- **THEN** all required fields SHALL be present and all values SHALL conform to their type/enum constraints
