# Psych-Wiki-Obsidian — An AI-Maintained Knowledge Base for Psychology Researchers

[简体中文](README.md) | **English**

> Drop papers into `raw/`, and an AI distills them into a growing knowledge network — **the AI does the bookkeeping; you do the judging.**

This is an Obsidian knowledge-base framework built on [Karpathy's LLM Wiki idea](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), **designed specifically for psychology researchers**. It tackles three concrete pains: papers you read and forget, notes scattered everywhere, and AI summaries you don't quite dare to trust. A single rulebook written for AI ([SCHEMA.md](SCHEMA.md)) lets any agent (Claude Code / Codex / …) maintain your knowledge to the same standard — while the authority to *trust* anything stays firmly with you.

![Three-layer architecture and data flow](assets/fig-1-architecture.png)

## ✨ Three Core Features

1. **A psychology research backbone** — concepts aren't a flat pile; they're organized by the four research roles `theory / construct / method / measure`, mirroring how researchers actually think.
2. **A dual-track trust model** — every AI-written page declares *what kind of source it came from* (`origin`) and *whether you have verified it* (`status`). AI-generated content can never masquerade as verified fact.
3. **Traceability all the way down** — every claim can be walked back through wikilinks: concept page → source summary page → the original PDF in `raw/`.

## 🏛 Design Philosophy

### Three layers

| Layer | Responsibility | Written by |
|---|---|---|
| `raw/` | Archive of original materials, **append-only**, the single source of truth | You |
| `wiki/` | The knowledge layer distilled from raw (source summaries, concepts, entities, comparisons, overviews) | AI |
| `meta/` | Index and operation log — the catalog and timeline of the whole vault | AI |

### Three principles

- **Traceable**: every important claim leads back to a source page, and ultimately to the original file in `raw/`.
- **Compounding**: new pages join the network through wikilinks, frontmatter, and the index — the vault gets more valuable the more you use it.
- **Growable**: a page may start as a one-sentence stub and grow into a full page as new sources arrive; nothing has to be written perfectly the first time.

### A constitution

[SCHEMA.md](SCHEMA.md) is the single authoritative rulebook: directory layout, page naming, frontmatter fields, and exactly when the AI must ask you first — all pinned down in one file. Any agent must read it in full before touching anything. Swap the AI, keep the rules: the vault's consistency depends on no particular tool.

## 🔬 The Psychology Backbone

This is what sets the vault apart from generic knowledge bases. Facing a paper, a psychology researcher naturally distinguishes four kinds of things — theories, constructs, methods, and measures. The vault bakes that distinction into its structure: every psychology concept page must carry a `research_role`:

![The psychology knowledge backbone](assets/fig-2-psychology-backbone.png)

| research_role | Meaning | Examples |
|---|---|---|
| `theory` | A theory / theoretical perspective | Conditional Reasoning Theory, Social Cognitive Theory |
| `construct` | A latent construct / trait / variable to be measured | Aggression, Achievement Motivation |
| `method` | A measurement method / research paradigm / data-collection technique | Conditional Reasoning Tests, Assessment Centers, Eye-tracking |
| `measure` | A specific test / instrument / scale | CRT-A, Buss-Perry Aggression Questionnaire |
| `concept` | Fallback: phenomena / frameworks / methodological concepts | Faking, Measurement Equivalence |

The four roles are connected into a queryable network. An example from this very vault:

> **Conditional Reasoning Theory** (theory) claims that personality shapes unconscious justification mechanisms → those mechanisms make **Aggression** (construct) measurable → the concrete instrument is the **CRT-A** (measure) → which belongs to the **Conditional Reasoning Test** (method) hub, sharing development standards with its siblings CRT-P, CRT-RMS, and others.

What this backbone buys you in practice:

- **Browse the way you think**: overview dashboards group pages by role automatically; "all instruments measuring aggression" or "every scale under the CRT method" is a single query.
- **A minimum template per role**: construct pages must have *Definition / Measurement / Sources*; measure pages must have *Construct & Dimensions / Reliability & Validity / Sample Items*; theory pages must have *Core Claims*. Pages distilled from different papers stay structurally comparable.
- **A safe fallback**: classification follows an explicit decision order, and anything genuinely ambiguous goes to `concept` — no over-inference for the sake of taxonomy.

## 🔒 The Dual-Track Trust Model

The biggest trap of AI-era knowledge management: everything the AI writes *looks* right. The vault manages "what kind of source" and "verified or not" as two orthogonal fields:

![The dual-track trust model](assets/fig-3-trust-matrix.png)

| | `status: needs-review` | `status: verified` |
|---|---|---|
| **`origin: solid`** (real materials) | Freshly distilled from a paper, awaiting your eyes | Literature knowledge you have checked ✅ |
| **`origin: ai-generated`** (AI synthesis) | AI synthesis or AI-assisted drafts — handle with most care | AI synthesis you endorsed (still labeled AI-generated) |

Hard rules: every new AI-created page starts as `needs-review`; only you can promote it to `verified` after reading it yourself; and verification can never "launder" AI-generated content into real material — `verified` changes the review state, never the origin.

## 🔁 Three Workflows

![The three workflows](assets/fig-4-workflows.png)

| Workflow | You say | The AI does |
|---|---|---|
| **Ingest** | "Ingest this PDF in `Inbox/`" | Reads the paper → confirms a page plan with you → archives the original into a `raw/` source package → writes a source summary → distills concept pages (auto-identifying theory/construct/method/measure nodes) → updates index and log |
| **Query** | "Based on the vault, how strong is the anti-faking evidence for CRTs?" | Locates pages via the index → traces back to raw originals when needed → answers while distinguishing verified from unverified content → never writes back without asking |
| **Lint** | "Run a health check on the vault" | Scans everything: frontmatter compliance, broken links, naming violations, missing template sections, orphan pages → produces a severity-graded report → fixes only after your confirmation |

Every destructive operation (batch renames, moves, deletions) requires a proposed list and your explicit confirmation first.

## 🚀 Getting Started

### 1. Get the vault

Clone this repository (or use it as a template) and open the folder in [Obsidian](https://obsidian.md).

### 2. Enable the overview dashboards (optional but recommended)

Overview pages use Dataview: install the **Dataview** plugin → enable **JavaScript queries** in its settings → enable the CSS snippet `.obsidian/snippets/overview-cards.css`.

### 3. Connect an AI agent (either way works)

The vault is plain text — **any agent that can read/write the folder and follow SCHEMA will do**:

- **Option A · Inside Obsidian**: install the community plugin Claudian, point it at your Claude Code / Codex CLI, and chat from the sidebar panel.
- **Option B · Terminal**: start Claude Code / Codex with this vault as the working directory; the root-level `CLAUDE.md` / `AGENTS.md` automatically routes the agent to SCHEMA.

### 4. The daily loop

1. **Collect**: found a good paper → drop it in `Inbox/`; your own notes → `raw/notes/`.
2. **Distill**: tell the AI "ingest the Inbox". It proposes a page plan first and only acts after your nod.
3. **Browse**: role-grouped dashboards in `wiki/overview/`; the Obsidian graph view for the knowledge network; `meta/index.md` for the full catalog.

## 📁 Repository Layout

```
raw/                  Original materials (read-only once archived)
  papers/             Paper source packages (one folder per paper: PDF + fulltext MD + images)
  articles/           Article source packages (web articles, blog posts, …)
  notes/              Your own class notes / reading notes / ideas
  media/              Standalone cross-source media
wiki/                 Knowledge layer (AI-maintained)
  sources/            Source summary pages (one per paper/article)
  concepts/           Concept pages (subdivided by research_role)
  entities/           Entity pages (people, books, projects)
  comparisons/        Comparison & cross-domain pages
  overview/           Auto-generated dashboards
meta/                 Maintenance layer (AI-maintained)
  index.md            Full catalog
  log.md              Operation log (who changed what, when)
Inbox/                To-be-processed materials (archived into raw/ after ingest)
SCHEMA.md             ⭐ The single authoritative rulebook — the AI's "constitution"
CLAUDE.md / AGENTS.md Entry signposts for the respective agents
```

## 🙏 Acknowledgements

- The structural idea comes from [Andrej Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
- Thanks to [Claude Code](https://claude.com/claude-code) and [Codex](https://openai.com/codex) — the rulebook's iteration and the vault's day-to-day maintenance are done by them.

## 📄 License

Released under the [MIT License](LICENSE).

---

*The single source of truth for rules is [SCHEMA.md](SCHEMA.md); this file only explains things to humans.*
