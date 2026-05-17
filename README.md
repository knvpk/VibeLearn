# vibe_learn

A curriculum-driven software learning tutor for Claude Code. It builds your mental model — not just answers questions — by teaching one concept at a time, saving session notes, and tracking your progress across an entire topic.

---

## How It Works

`vibe_learn` is a skill. Once installed, every session is driven by a `vibe_learn.config.yaml` file you place in your project root. That file tells the tutor what subject you're studying, where your curriculum files live, and what language and framework to use for code examples.

The tutor reads your progress before each session, teaches using a Socratic approach (analogy first, simple example next, complexity added gradually), and saves structured notes once you confirm understanding. Progress is tracked per-concept with statuses (`Not started` → `In progress` → `Done`) and ISO timestamps.

---

## Quick Start

### 1. Install the skill

Place `SKILL.md` in your project's `.claude/skills/` directory (or wherever Claude Code loads skills from).

### 2. Create your config

Copy the template and fill in your topic:

```bash
cp assets/vibe_learn.config.template.yaml vibe_learn.config.yaml
```

Minimal config:

```yaml
topic: "Agentic AI Engineering"

curriculum:
  root: theory/
  concepts_file: theory/concepts.json
  plan_file: theory/plan.json
  progress_file: theory/progress.json
  sources_file: theory/sources.json
  concepts_dir: theory/concepts/
  concept_schema: .claude/schemas/concept_meta.json

examples:
  language: Python
  framework: FastAPI
  idioms:
    - async/await
    - dependency injection
    - Pydantic models
```

### 3. Set up curriculum files

Copy the starter files into your `theory/` directory:

```bash
mkdir -p theory/concepts
cp assets/starter/concepts.json theory/concepts.json
cp assets/starter/plan.json     theory/plan.json
cp assets/starter/progress.json theory/progress.json
cp assets/starter/sources.json  theory/sources.json
```

Edit `concepts.json` and `plan.json` to reflect your actual curriculum.

### 4. Start learning

Invoke the skill in Claude Code:

```
/vibe_learn
```

Then say anything like:

- `next topic`
- `teach me async/await`
- `what is dependency injection?`
- `I want to learn about RAG`
- `continue`

---

## Project Layout

```
your-project/
├── vibe_learn.config.yaml          # Required — config the tutor reads on every startup
├── theory/
│   ├── concepts.json          # Hierarchical topic map with priorities
│   ├── plan.json              # Phase ordering, durations, prerequisites
│   ├── progress.json          # Per-concept status, timestamps, notes flag
│   ├── sources.json           # Registry of external resources (URLs, articles)
│   └── concepts/
│       └── <concept-id>/      # One folder per concept (flat, no nesting)
│           ├── main.md        # Entry point: frontmatter + overview + links
│           ├── internals.md   # How it works under the hood
│           ├── examples.md    # Code walkthroughs
│           └── patterns.md    # Patterns, pitfalls, tips
└── .claude/
    └── schemas/
        └── concept_meta.json  # Frontmatter schema for main.md files
```

---

## Curriculum Files

### `concepts.json` — Topic Map

Defines every concept the tutor can teach, organized into phases with priorities.

```json
{
  "title": "Agentic AI Engineering Curriculum",
  "phases": [
    {
      "id": "phase_1",
      "title": "Foundations",
      "topics": [
        {
          "id": "async_python",
          "title": "Async Python (asyncio, aiohttp)",
          "priority": "P1",
          "subtopics": ["event_loop", "coroutines", "tasks"]
        }
      ]
    }
  ]
}
```

**Best practices:**
- Use `snake_case` IDs — they become folder names and progress keys
- Assign priorities (`P1`, `P2`, `P3`) to let the tutor surface high-value concepts first
- Keep subtopics as string IDs, not nested objects — depth is expressed in `related_concepts` frontmatter

### `plan.json` — Phase Ordering

Declares phase sequence, estimated durations, and prerequisites.

```json
{
  "phases": [
    {
      "id": "phase_1",
      "title": "Foundations",
      "duration_weeks": "2–4",
      "order": 1,
      "prerequisites": []
    },
    {
      "id": "phase_2",
      "title": "Intermediate",
      "duration_weeks": "3–5",
      "order": 2,
      "prerequisites": ["phase_1"]
    }
  ]
}
```

**Best practices:**
- Keep `order` sequential — the tutor uses it to find the next topic
- List prerequisites by phase ID so the tutor can warn if you jump ahead

### `progress.json` — Status Tracking

One entry per concept. The tutor writes this file automatically — you rarely need to edit it by hand.

```json
{
  "async_python": {
    "status": "Done",
    "started_at": "2025-06-01",
    "completed_at": "2025-06-03",
    "notes": true
  },
  "dependency_injection": {
    "status": "In progress",
    "started_at": "2025-06-04",
    "completed_at": null,
    "notes": false
  }
}
```

Status lifecycle: `Not started` → `In progress` → `Done`

**Best practices:**
- Never set `status: "Done"` manually for a concept you haven't actually learned — the tutor will skip teaching it
- Set `notes: false` to force the tutor to re-save notes on the next session even if status is `Done`

### `sources.json` — External Resource Registry

```json
{
  "sources": [
    {
      "url": "https://docs.python.org/3/library/asyncio.html",
      "description": "Official asyncio reference",
      "concepts": ["async_python", "event_loop"]
    }
  ]
}
```

The tutor checks this before doing a web search and adds new links here automatically after fetching URLs you provide.

---

## Teaching Sessions

### Trigger Phrases

The skill activates on any of these (and many natural variations):

| Intent | Example phrases |
|--------|----------------|
| Start / continue | `next topic`, `what's next`, `continue`, `let's learn` |
| Teach a concept | `teach me X`, `explain X`, `what is X`, `I want to learn X` |
| Revisit | `review X`, `go deeper on X`, `revisit async` |
| Confirm understanding | `got it`, `makes sense`, `that lands` |

### Session Flow

1. **Startup** — tutor reads `vibe_learn.config.yaml` and checks your progress
2. **Teach** — analogy first, then simplest example, then complexity
3. **Check** — "Does that make sense before we go deeper?"
4. **Save** — once you confirm understanding, notes are written automatically
5. **Next** — "Saved. Ready for the next one?"

### How Notes Are Saved

When you confirm understanding, the tutor:
- Creates `theory/concepts/<id>/main.md` with YAML frontmatter
- Splits deep-dives, examples, and diagrams into sibling files
- Sets `notes: true` and updates status/timestamps in `progress.json`

**`main.md` structure:**

```markdown
---
id: async_python
name: "Async Python (asyncio, aiohttp)"
description: "Non-blocking I/O using Python's event loop."
related_concepts:
  - event_loop
  - streaming_tool_use
tags:
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
- ...
```

**Best practices:**
- Keep `main.md` under 80 lines — deep content goes in split files
- Always link split files from `main.md` so the folder is navigable
- Concept folders are always flat siblings — never nest one inside another

---

## URL / Blog Post Ingestion

Paste any URL into the chat and the tutor will:

1. **Fetch** the page with `WebFetch` and summarize it
2. **Map** the content to concepts in your curriculum (asks before saving)
3. **Save** to the matching concept folder with a `## Source` section and attribution comment on code snippets
4. **Register** the URL in `sources.json` with concept links

**Best practices:**
- Confirm the proposed concept mapping before the tutor writes anything — it will always ask
- If an article covers more than three distinct concepts, tell the tutor which ones to prioritize
- URLs that return errors are reported clearly; the tutor offers a `WebSearch` fallback

---

## Diagrams

When concepts involve flows, architectures, or lifecycles, diagrams are generated automatically.

| Format | When to use |
|--------|-------------|
| **Mermaid** | Flowcharts, sequence diagrams, state machines, DAGs — renders in VS Code and GitHub |
| **D2** | Architecture diagrams with layout control or multi-container systems |
| **SVG** | Custom visual metaphors only |

Each diagram lives in its own file named after what it shows (e.g. `tokenization_pipeline.md`, `attention_mechanism.md`). Diagrams are linked inline from the exact section that references them — not just from `main.md`.

---

## Curriculum Upkeep

At the end of sessions, the tutor may ask:

> "I noticed `circuit_breaker` isn't in the concepts file — want me to add it under `resilience_patterns`?"

**Best practices:**
- Never ignore these prompts — gaps in `concepts.json` cause the tutor to miss relationships
- The tutor will never silently edit `concepts.json` or `concept_meta.json` — always confirm first
- If a concept evolves in meaning as you learn, update its description in `concepts.json` so future sessions reflect your understanding

---

## Tips for Better Sessions

- **Be specific about gaps** — "I understand the what but not the why" is more useful than "explain again"
- **Use the codebase** — say "show me this in the current codebase" to anchor abstract concepts to real code
- **Let the tutor set pace** — it waits for your confirmation before advancing; don't rush past checkpoints
- **One concept at a time** — starting sessions with "teach me everything about X" is fine; the tutor will chunk it
- **Mark concepts Done honestly** — saying "got it" when you don't prevents the tutor from deepening your understanding on future revisits

---

## Requirements

- Claude Code with internet access (`WebFetch`, `WebSearch`)
- `vibe_learn.config.yaml` in your project root
- `theory/` directory with `concepts.json`, `plan.json`, `progress.json`, `sources.json`

---

## File Reference

| File | Purpose |
|------|---------|
| `SKILL.md` | Skill definition — do not edit unless customizing tutor behavior |
| `vibe_learn.config.yaml` | Your config — topic, paths, language, framework, idioms |
| `theory/concepts.json` | Hierarchical topic map |
| `theory/plan.json` | Phase ordering and prerequisites |
| `theory/progress.json` | Per-concept status and timestamps |
| `theory/sources.json` | External resource registry |
| `assets/vibe_learn.config.template.yaml` | Starter config template |
| `assets/starter/` | Starter JSON files for new projects |
| `assets/schemas/concept_meta.json` | Frontmatter schema for `main.md` |
| `references/url-workflow.md` | URL ingestion rules |
| `references/saving-content.md` | Content saving and folder layout rules |
| `references/diagrams.md` | Diagram format and naming rules |
