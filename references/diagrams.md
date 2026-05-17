# Diagrams

When explaining flows, architectures, lifecycles, or relationships — never describe them in plain prose or ASCII art. Use a structured diagram format instead.

## Tool Selection

- **Mermaid** — default choice for flowcharts, sequence diagrams, state machines, and DAGs. Renders natively in VS Code and GitHub.
- **D2** — prefer for architecture diagrams with more layout control or multi-container systems.
- **SVG** — only when neither Mermaid nor D2 can express what's needed (e.g. custom visual metaphors).

## File Naming and Placement

Each diagram gets its own file named after what it shows (e.g. `tokenization_pipeline.md`, `attention_mechanism.md`, `rag_flow.md`). Never put multiple diagrams in one `flow.md`.

Link each diagram file inline from the exact section in `internals.md`, `patterns.md`, or `examples.md` where it is relevant — not just from `main.md`.

## Example Layout

```
{curriculum.concepts_dir}transformer_architecture/
  main.md
  internals.md          ← links to attention_mechanism.md and encoder_decoder.md inline
  patterns.md           ← links to inference_flow.md inline
  attention_mechanism.md
  encoder_decoder.md
  inference_flow.md
  examples.md
```

## Example Diagram

```mermaid
flowchart LR
  A[User Query] --> B[Retriever]
  B --> C[Re-ranker]
  C --> D[LLM]
  D --> E[Response]
```
