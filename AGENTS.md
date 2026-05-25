# LLM Wiki

A personal knowledge base maintained by an AI agent (Cursor, Claude Code, or any tool that reads this schema).
Based on Andrej Karpathy's [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern. For Claude Code, copy or symlink this file to `CLAUDE.md` at the project root (see README).

This file is the **schema** — it tells the agent how the wiki is structured, what conventions to follow, and what workflows to run. Co-evolve it with your domain as you learn what works. If this file conflicts with a generic chat habit, **this file wins**.

## Core idea

Most LLM + document workflows are RAG: retrieve chunks at query time and answer. Nothing accumulates; every subtle question re-derives knowledge from raw files.

This pattern is different: the agent **incrementally builds and maintains a persistent wiki** — structured, interlinked markdown between you and `raw/`. When you add a source, the agent reads it, extracts key information, and integrates it into the wiki (new pages, updated concepts, flagged contradictions). Cross-references and synthesis are compiled once and kept current.

**The wiki is a compounding artifact.** Contradictions are noted. Links exist before you ask. Good questions and answers get filed back so explorations compound like ingested sources.

## Roles

- **Human**: Curate sources in `raw/`, ask questions, guide emphasis during ingest, decide what to trust, optionally browse in Obsidian.
- **Agent**: Read sources, write and update `wiki/`, maintain `index.md` and `log.md`, cross-reference, lint, and bookkeeping. Do not edit `raw/`.

## Purpose

This wiki is a structured, interlinked knowledge base for **[YOUR TOPIC]**.
Replace `[YOUR TOPIC]` with your domain when you copy this template (e.g. "LLM quantization methods" or "multimodal LLM challenges").

## Architecture

Three layers:

```
raw/              -- immutable source documents (never modify)
wiki/             -- agent-maintained markdown pages
AGENTS.md         -- this schema (you are reading it)
```

Optional:

```
wiki/outputs/     -- one-off query artifacts (tables, slide decks) if requested
raw/assets/       -- local images clipped from web articles (optional)
```

## Folder structure

```
raw/              -- source documents (immutable -- never modify these)
wiki/             -- markdown pages maintained by the agent
wiki/index.md     -- categorized table of contents
wiki/log.md       -- append-only record of all operations
wiki/outputs/     -- optional; saved comparisons, decks, charts
```

## Sources

- Drop PDFs, `.md`, `.txt`, and other readable files into `raw/`.
- **Obsidian Web Clipper** is useful for saving web articles as markdown into `raw/`.
- For image-heavy articles: store assets under `raw/assets/` and reference them from clipped markdown. Read text first, then view referenced images when needed for context.
- If the same document appears under multiple filenames in `raw/`, list **every alias** in each affected page's **Sources** field.

## Page types

- **Source page** — one summary per work (paper, article, book chapter), named after the source (e.g. `attention-is-all-you-need.md`).
- **Concept page** — one per major idea or entity, deduplicated across sources (e.g. `transformer-attention.md`).
- **Overview page** — synthesis across many sources for a subfield (optional; valuable in dense domains).
- **Guided tour** — optional `wiki/guided-tour.md` with long reading paths; link from index after the wiki has roughly 10+ source pages.

## Page format

Every wiki page (including `index.md` and `log.md`) should follow this structure:

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

## Index format

`wiki/index.md` is content-oriented. Keep it categorized with section headers, for example:

- **Maintenance** — `[[log]]`, optional `[[guided-tour]]`
- **Overviews** — hub pages and domain summaries
- **Sources** — one line per source summary page
- **Concepts** — grouped by topic where helpful

Each entry: `[[page-name]]` — one-line description. Update on every ingest or structural change.

## Log format

`wiki/log.md` is chronological and append-only. Start each entry with a parseable header:

```markdown
## [YYYY-MM-DD] ingest | filename.pdf

- Created [[source-page]]; updated [[concept-a]], [[concept-b]]
- Refreshed index sections: Sources, Concepts
```

Operations: `ingest` | `query` | `lint` | `edit`. Include subject (filename, question topic, or "wiki audit") and bullets listing pages touched.

## Ingest workflow

When the user adds a source to `raw/` and asks you to ingest it:

1. Read the full source document
2. Discuss key takeaways with the user before writing anything (unless batch mode below)
3. Create or update a **source page** in `wiki/` named after the work
4. Create or update **concept pages** for each major idea or entity
5. Add wiki-links (`[[page-name]]`) to connect related pages
6. Update `wiki/index.md` in the correct sections
7. Append a log entry using the log format above

A single source may touch 10–15 wiki pages. That is normal.

### Batch ingest

When the user asks to ingest all new files in `raw/`:

1. Compare `raw/` against `wiki/log.md` and `wiki/index.md` to find files not yet ingested
2. Confirm the list with the user (or proceed if they asked for unattended batch)
3. Process each file: lighter per-file discussion is acceptable; group related files by topic when useful
4. One log entry per source (or one grouped entry if the user prefers)

### Duplicate raw files

If two or more files in `raw/` are the same work under different names, use one source page and list every filename in **Sources** on that page and on related concept pages.

## Citation rules

- Every factual claim should reference its source file
- Use the format `(source: filename.pdf)` after the claim
- If two sources disagree, note the contradiction explicitly
- If a claim has no source, mark it as needing verification

## Question answering

When the user asks a question:

1. Read `wiki/index.md` first to find relevant pages
2. Read those pages and synthesize an answer
3. Cite specific wiki pages in your response
4. If the answer is not in the wiki, say so clearly
5. If the answer is valuable, offer to save it as a new wiki page or under `wiki/outputs/`

Good answers should be filed back into the wiki so they compound over time.

### Filing answers

- **Durable knowledge** (definitions, comparisons, synthesis) → new or updated pages in `wiki/`, linked from index
- **One-off artifacts** (slide deck, large table, chart) → `wiki/outputs/` when the user wants that format; still update `log.md`
- Supported output forms when requested: markdown page, comparison table, [Marp](https://marp.app/) slides, charts — prefer saving over leaving answers only in chat

## Lint

When the user asks you to lint or audit the wiki:

- Check for contradictions between pages
- Find orphan pages (no inbound links from other pages)
- Identify concepts mentioned in pages that lack their own page
- Find **missing cross-references** (concept discussed on page B but no `[[concept]]` link)
- Flag claims that may be outdated based on newer sources
- Note **data gaps** (thin coverage); suggest new sources or web search if the user allows
- Check that all pages follow the page format above
- Check that `index.md` sections are current and `log.md` entries use the standard header format
- Report findings as a numbered list with suggested fixes
- End with **3–5 proactive questions** to investigate next, derived from gaps or contradictions

## Optional patterns (mature wikis)

Use when the wiki grows large or the domain is dense:

- **`wiki/guided-tour.md`** — reading paths and syllabus-style navigation; link under Maintenance in index
- **Overview hub pages** — 1–2 synthesis pages early (e.g. how subtopics fit together)
- **`wiki/outputs/`** — query artifacts that are not core concept pages

## At scale

- Up to roughly 100 sources and hundreds of pages: reading `index.md` first, then drilling into pages, usually suffices
- Beyond that: consider a local search tool (e.g. [qmd](https://github.com/tobi/qmd)) — see README for setup notes
- The wiki is a git repo of markdown files; version history is optional and controlled by the human — do not commit unless asked

## Rules

- Never modify anything in the `raw/` folder
- Always update `wiki/index.md` and `wiki/log.md` after changes
- Keep page names lowercase with hyphens (e.g. `machine-learning.md`)
- Write in clear, plain language
- When uncertain about how to categorize something, ask the user

For Obsidian graph view, Web Clipper, and optional plugins, see [README.md](README.md).
