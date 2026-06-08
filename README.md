---
type: guide
updated: 2026-06-08
---

# Psych-Wiki-Obsidian

> An Obsidian vault template for psychology researchers who want an LLM-maintained wiki: put sources in `raw/`, let an AI agent distill them into `wiki/`, and keep the whole system governed by `SCHEMA.md`.

This project adapts Andrej Karpathy's [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) idea for psychology and psychometrics workflows. It is a **template**, not a public dump of anyone's private notes or PDFs.

## Why This Exists

Psychology knowledge work is source-heavy. A useful wiki needs to distinguish papers, constructs, theories, methods, measures, and comparisons without turning the folder tree into a taxonomy maze.

This template keeps the file structure simple and moves the research ontology into frontmatter:

```text
raw/                  original materials, read-only to agents
  papers/
  articles/
  notes/
  media/
wiki/                 distilled knowledge layer
  sources/            one page per source
  concepts/           theory / construct / method / measure / concept
  entities/           people, labs, books, projects when they matter
  comparisons/        comparisons and cross-domain bridges
  overview/           Dataview dashboards
meta/                 maintenance layer
  index.md
  log.md
Inbox/                temporary intake folder
SCHEMA.md             the rules agents must read first
AGENTS.md             Codex entrypoint
CLAUDE.md             Claude entrypoint
```

## Psychology Schema

Every `wiki/concepts/` page has a `kind`:

```yaml
kind: theory      # theory or theoretical perspective
kind: construct   # latent variable / trait / psychological attribute
kind: method      # measurement method / paradigm / technique
kind: measure     # specific test / scale / questionnaire / task
kind: concept     # fallback for phenomena, frameworks, methodological concepts
```

Measure pages also use relationship fields:

```yaml
operationalizes: []  # which construct this measure operationalizes
method: []           # which method node it belongs to
based_on: []         # optional theory / method / technology foundation
```

The goal is to make questions like these easy to answer:

- Which measures operationalize this construct?
- Which concrete tests belong under this method?
- Which theories support this measurement route?
- Which sources feed this concept page?

## Quick Start

1. Clone this repository.
2. Open the folder as an Obsidian vault.
3. Install the Dataview community plugin and enable JavaScript queries.
4. Enable the CSS snippets under `.obsidian/snippets/`, especially `overview-cards`.
5. Read `SCHEMA.md` before asking an AI agent to ingest, query, lint, or modify files.
6. Put materials in `Inbox/` or `raw/`, then ask your agent to ingest them.

## Optional Sorting

The included `sortspec.md` is for the Obsidian plugin **Custom File Explorer sorting**. It keeps the `wiki/` folder visually ordered without renaming folders:

```text
concepts
overview
sources
comparisons
entities
```

If you do not use that plugin, the file is harmless.

## What Is Intentionally Empty

The template tracks only structure and rules. It does not include private notes, PDFs, attachments, or generated knowledge pages.

The `.gitignore` keeps these folders from accidentally becoming a public knowledge dump:

```text
raw/*
Inbox/*
wiki/sources/*
wiki/concepts/*
wiki/entities/*
wiki/comparisons/*
Attachments/
.claudian/
```

Each empty folder has a `.gitkeep` so the vault opens with the intended structure.

## License

MIT. See [LICENSE](LICENSE).
