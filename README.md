# Local Wiki Template

A local, Cursor-maintained personal knowledge base built from Markdown, PDF, and text sources. Drop raw documents into a folder, ask the agent to ingest them, and grow a structured, interlinked wiki that compounds over time.

## Inspired by Karpathy's LLM Wiki

This template follows [Andrej Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern: persistent raw sources, a compiled wiki with cross-references, and three core operations—**ingest**, **query**, and **lint**. Unlike one-off RAG per question, the wiki accumulates and stays current as you add sources.

| Resource | Link | Role |
|----------|------|------|
| Original spec (gist) | [karpathy/442a6bf…](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) | Core pattern: ingest, query, lint; raw → wiki → schema |
| Related tweet | [x.com/karpathy/status/2039805659525644595](https://x.com/karpathy/status/2039805659525644595) | Public announcement of the idea |

**See also** (other implementations, different stacks):

- [danvega/karpathy-wiki](https://github.com/danvega/karpathy-wiki) — Java Spring Shell CLI with automated compile and research agents
- [kytmanov/obsidian-llm-wiki-local](https://github.com/kytmanov/obsidian-llm-wiki-local) — Obsidian vault + local LLM (Ollama) pipeline

This template is a **lightweight, Cursor + `AGENTS.md` variant**—not a fork of those projects. You drive workflows with natural-language prompts in Cursor Agent mode.

## Quick start

1. **Copy** this `template` folder to a new directory (e.g. `my-topic-wiki`).

2. **Edit** [`AGENTS.md`](AGENTS.md) — replace `[YOUR TOPIC]` in the **Purpose** section with your domain (e.g. "LLM quantization methods").

3. **Scaffold is included** — you already have:

```
my-topic-wiki/
├── AGENTS.md            # schema: agent contract (read this first)
├── Agent.md             # points to AGENTS.md (backward compatibility)
├── README.md
├── .cursor/rules/       # optional: loads AGENTS.md in Cursor
├── raw/                 # drop PDFs, .md, .txt here
└── wiki/
    ├── index.md         # categorized table of contents
    └── log.md           # append-only operation log
```

4. **Open the folder in Cursor** and use **Agent mode**. The agent follows [`AGENTS.md`](AGENTS.md). A Cursor rule in `.cursor/rules/wiki.mdc` points agents at the schema automatically.

5. **Add a source** to `raw/`, then prompt: *"Ingest `raw/your-file.pdf` into the wiki."*

## Folder layout

```
raw/              -- source documents (immutable; never modify these)
wiki/             -- markdown pages maintained by the agent
wiki/index.md     -- categorized table of contents
wiki/log.md       -- append-only record of operations
wiki/outputs/     -- optional: saved comparisons, decks, charts
AGENTS.md         -- schema (structure, workflows, conventions)
```

- **`raw/`** — Your originals. The agent reads them but never edits them.
- **`wiki/`** — Generated and updated pages, linked with `[[wiki-links]]`.
- **`wiki/index.md`** — Catalog by section (Maintenance, Overviews, Sources, Concepts).
- **`wiki/log.md`** — Chronological record with parseable headers (`## [date] operation | subject`).
- **`AGENTS.md`** — Single source of truth for the agent; co-evolve with your domain.

## How to use

### Ingest

Add a file to `raw/` (e.g. `paper.pdf`, `notes.md`), then ask Cursor:

- *"Ingest `raw/paper.pdf` into the wiki."*
- *"Ingest all new files in `raw/`."*

The agent will:

1. Read the full source
2. Discuss key takeaways with you before writing (lighter in batch mode)
3. Create or update a source page and concept pages
4. Add `[[wiki-links]]` between related pages
5. Update `wiki/index.md` and append to `wiki/log.md`

A single source often updates **10–15 pages**. That is expected.

### Query

Ask questions against what is already in the wiki:

- *"What does the wiki say about X?"*
- *"How do these two papers compare on Y?"*

The agent reads `wiki/index.md` first, then relevant pages, cites specific wiki pages, and says clearly if the answer is not in the wiki. Valuable answers can be saved as new wiki pages (or under `wiki/outputs/` for decks and tables) so knowledge compounds.

### Lint

Periodically audit structure and consistency:

- *"Lint the wiki."*
- *"Audit the wiki for orphans and contradictions."*

The agent checks contradictions, orphans, missing concept pages, missing cross-references, stale claims, format compliance, data gaps, and ends with suggested next questions. Results are a numbered list with fixes.

## Page format

Every wiki page should follow this structure (full contract in [`AGENTS.md`](AGENTS.md)):

```markdown
# Page Title

**Summary**: One to two sentences describing this page.

**Sources**: List of raw source files this page draws from.

**Last updated**: Date of most recent update.

---

Main content goes here. Use clear headings and short paragraphs.

Link to related concepts using [[wiki-links]] throughout the text.

## Related pages

- [[related-concept-1]]
- [[related-concept-2]]
```

## Rules and citations

- **Never modify `raw/`** — sources stay immutable.
- **Always update** `wiki/index.md` and `wiki/log.md` after changes.
- **Page names**: lowercase with hyphens (e.g. `machine-learning.md`).
- **Citations**: append `(source: filename.pdf)` after factual claims.
- Note contradictions when two sources disagree; mark unsourced claims as needing verification.
- When unsure how to categorize something, the agent should ask you.

## Optional: Obsidian

You can open the wiki folder in [Obsidian](https://obsidian.md) for graph view and navigation via `[[wiki-links]]`. Content stays plain Markdown; Obsidian is optional.

**Tips** (from Karpathy's gist):

- **Web Clipper** — save articles as markdown into `raw/`.
- **Local images** — set attachment folder to `raw/assets/`; download images after clipping so the agent can reference them reliably.
- **Graph view** — see hubs, orphans, and clusters.
- **Marp** — markdown slide decks from wiki content (Obsidian plugin).
- **Dataview** — query YAML frontmatter if you add it to pages later (optional).

Some wikis in this repo include a `.obsidian/` folder for vault settings—copy that pattern if you want the same setup.

## Optional: At scale

- Roughly up to ~100 sources: `index.md` + page reads is enough.
- Larger wikis: consider [qmd](https://github.com/tobi/qmd) for local hybrid search (CLI or MCP).
- Use **git** for version history when you want it; the agent does not commit unless you ask.

## Optional: Mature wiki patterns

After the wiki grows:

| Pattern | When |
|---------|------|
| `wiki/guided-tour.md` | Long reading paths; link from index under Maintenance |
| Overview hub pages | Dense domains; 1–2 synthesis pages early |
| `wiki/outputs/` | One-off comparisons, Marp decks, charts from queries |
| Duplicate `raw/` filenames | Same PDF under two names — list all aliases in **Sources** |

See [`AGENTS.md`](AGENTS.md) for full guidance.

## Customization

| What to change | Where |
|----------------|--------|
| Topic and domain | **Purpose** in [`AGENTS.md`](AGENTS.md) |
| Ingest, query, lint behavior | Full [`AGENTS.md`](AGENTS.md) (schema) |
| Human-facing overview | This `README.md` |

Keep detailed agent rules in `AGENTS.md`; keep this README focused on how humans set up and use the wiki. [`Agent.md`](Agent.md) only redirects to `AGENTS.md` for older references.

## What you need

- [Cursor](https://cursor.com) with Agent mode
- Local Markdown/PDF/text sources
- No API keys or cloud services required—the wiki is local files only
