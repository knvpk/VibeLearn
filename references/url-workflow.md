# URL / Blog Post Workflow

When the user provides a URL (blog post, article, documentation page, or any external resource), follow these four steps.

## Step 1 — Fetch and summarize

1. Use `WebFetch` to retrieve the full content of the URL
2. Extract the core concepts, key takeaways, and any code examples present
3. Present a one-paragraph summary and a bullet list of concepts covered
4. Ask: "This covers [X, Y, Z]. Should I map these to the curriculum?"
5. Wait for confirmation before proceeding

## Step 2 — Map to curriculum

1. Read `{curriculum.concepts_file}` to find the best-fit concept(s) for the content
2. For each concept found:
   - If it **already has a folder** under `{curriculum.concepts_dir}<id>/`, plan to enrich it — add a new split file (e.g., `external_notes.md`) or extend an existing one
   - If it **exists in the concepts file but has no folder yet**, treat it as a new concept to create from scratch
   - If it **doesn't exist in the concepts file** at all, propose adding it with a suggested parent/phase placement — show the user where it would sit and ask before adding
3. Show the mapping clearly: "I'll save this under `{curriculum.concepts_dir}<id>/` — does that placement make sense?"
4. Wait for confirmation before writing anything

## Step 3 — Save the content

Follow the same saving rules as a taught session (see [saving-content.md](saving-content.md)):
- Write or update `<id>.md` with YAML frontmatter (read `{curriculum.concept_schema}` first)
- Add a `## Source` section at the bottom of `<id>.md` with the original URL and a one-line description of what the article covers
- Split sub-topics, code walkthroughs, and deep-dives into separate files prefixed with the concept ID (`<id>_internals.md`, `<id>_examples.md`, `<id>_patterns.md`, etc.) — never dump everything into `<id>.md`
- Link all split files from `<id>.md` using relative markdown links
- For code examples copied from the article, preserve them verbatim with a `<!-- source: <url> -->` attribution comment
- Add diagrams per the [Diagrams rules](diagrams.md) when the article covers flows, architectures, or lifecycles

## Step 4 — Update registries

- Add the URL to `{curriculum.sources_file}` with a short description and the concept ID(s) it maps to
- Update `{curriculum.progress_file}` for every concept saved: set `notes: true`; if status was `Not started`, advance it to `In progress` and set `started_at`; leave `Done` status unchanged
- Do **not** mark status `Done` from an article alone — that requires the learner to confirm understanding interactively

## Guardrails

- Never save content from a URL without first showing the summary and getting explicit confirmation on the concept placement
- If the URL is inaccessible or returns an error, report it clearly and offer to run a `WebSearch` for the topic instead
- If the article covers more than three distinct concepts, ask the user which ones to prioritize rather than bulk-saving everything at once
