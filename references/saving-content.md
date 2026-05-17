# Saving Content

All saved content lives under `{curriculum.root}`.

## Folder Rules

- Concept content goes under `{curriculum.concepts_dir}<concept-name>/` where `<concept-name>` is the snake_case ID from the concepts file
- `{curriculum.concepts_dir}` is **always flat** — no nesting. Even if a concept is a subtopic of another in the concepts file, it gets its own sibling folder at the same level as the parent, not inside it. Relationship is expressed via `related_concepts` in the frontmatter, not folder hierarchy.
- The entry point for every concept folder is `main.md`
- `main.md` must stay lean — structured overview with links, not a dump of everything:
  - Keep `main.md` under ~80 lines
  - Split sub-topics, deep-dives, code walkthroughs, and examples into separate files in the same folder (e.g., `examples.md`, `internals.md`, `patterns.md`)
  - Link every split file from `main.md` using relative markdown links

## `main.md` Structure

Every `main.md` must open with a YAML frontmatter block that conforms to `{curriculum.concept_schema}`. Before writing, read that schema to confirm required fields. Resolve all `related_concepts` IDs by scanning the concepts file. After saving, set `notes: true` in the progress file for the matching `id`.

All five fields are required — do not omit any:

```markdown
---
# {curriculum.concept_schema}
id: async_python                          # snake_case — matches folder name, concepts key, and progress key
name: "Async Python (asyncio, aiohttp)"   # human-readable title from concepts file
description: "One-sentence plain-English summary."
related_concepts:                         # IDs from concepts file — prerequisites, siblings, or follow-ons
  - sdk_basics
  - streaming_tool_use
tags:                                     # free-form cross-cutting themes
  - concurrency
  - async
---

# Async Python

One-paragraph plain-English summary.

## Contents
- [How it works](internals.md)
- [Examples](examples.md)
- [Patterns & pitfalls](patterns.md)

## Key Takeaways
- Bullet 1
- Bullet 2
```

## Progress File Updates

After every save, update `{curriculum.progress_file}`:
- Set `notes: true`
- Update `status` (`Not started` → `In progress` → `Done`)
- Set `started_at` on first teach (ISO date)
- Set `completed_at` when learner confirms Done
