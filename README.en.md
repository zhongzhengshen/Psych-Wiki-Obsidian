# Psych-Wiki-Obsidian — A Knowledge-Base Framework for Psychology Researchers

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Obsidian](https://img.shields.io/badge/Obsidian-7C3AED?logo=obsidian&logoColor=white)](https://obsidian.md)

[简体中文](README.md) | **English**

> This is an Obsidian knowledge-base framework built on [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) idea, designed for psychology students and researchers.
> It addresses these problems: papers read and then forgotten, notes scattered everywhere, knowledge that never connects into anything useful. The solution is a rulebook written for AI ([SCHEMA.md](SCHEMA.md)): whether the agent taking over is Claude Code, Codex, or some future agent, it maintains your knowledge to the same standard.

![Three-layer architecture and data flow](assets/fig-1-architecture.png)

## Design Philosophy

### The Single Rulebook

[SCHEMA.md](SCHEMA.md) is the vault's sole authoritative rulebook: how directories are organized, how pages are named, how frontmatter is filled in, and under what circumstances the AI must ask you first — all of it written down here as codified law. Any agent must read it in full before taking over. Swap the AI, keep the rules — the vault's consistency rests on this constitution, not on any particular tool.

### Three Principles

- **Traceable**: any piece of knowledge can be walked back to its origin along wikilinks: from concept page, to source summary page, and finally to the original material in `raw/`.
- **Compounding**: new pages join the existing network through wikilinks, frontmatter, and the index; knowledge multiplies through citation instead of sleeping in folders.
- **Growable**: a page may begin as a stub with a one-sentence definition and grow into a full page as new literature arrives. No page is required to be finished in one sitting, just as no idea is required to mature in one step.

### Three-Layer Directory Structure

```
raw/                  Original materials layer, in but never out — the single source of truth
  papers/             Paper source packages (one folder per paper: PDF + fulltext MD + images)
  articles/           Article source packages (web articles, blog posts, etc.)
  notes/              Class notes / reading notes / ideas
  media/              Standalone cross-source media
wiki/                 Knowledge layer distilled from raw, maintained by AI
  sources/            Source summary pages (one per source)
  concepts/           Concept pages (subdivided by research_role)
  entities/           Entity pages (people, books, projects)
  comparisons/        Comparison & cross-domain pages
  overview/           Auto-generated dashboards
meta/                 Maintenance layer — the vault's catalog and timeline
  index.md            Full catalog
  log.md              Operation log
Inbox/                To-be-processed materials (archived into raw/ after processing)
SCHEMA.md             The AI's single rulebook
CLAUDE.md / AGENTS.md Entry signposts for the respective agents
```

## The Psychology Research Backbone

This is the deepest difference between this vault and a generic knowledge base. In a psychology researcher's eyes, a paper naturally decomposes into four kinds of components: theories, constructs, methods, and measures. The vault writes that distinction into the structure of knowledge itself: every psychology concept page must carry a `research_role`.

![The psychology knowledge backbone](assets/fig-2-psychology-backbone.png)

| research_role | Meaning | Examples |
|---|---|---|
| `theory` | A theory / theoretical perspective | Conditional Reasoning Theory, Social Cognitive Theory |
| `construct` | A latent construct / trait / variable to be measured | Aggression, Achievement Motivation |
| `method` | A measurement method / research paradigm / data-collection technique | Conditional Reasoning Tests, Assessment Centers, Eye-tracking |
| `measure` | A specific test / instrument / scale | CRT-A, Buss-Perry Aggression Questionnaire |
| `concept` | Fallback: phenomena / frameworks / methodological concepts | Faking, Measurement Equivalence |

&nbsp;

The four roles are linked into a queryable network. For example:

> **Conditional Reasoning Theory** (theory) holds that personality and motives shape unconscious justification mechanisms; those mechanisms make **Aggression** (construct) measurable; the concrete instrument for that construct is the **CRT-A** (measure); and the CRT-A in turn belongs to the **Conditional Reasoning Test** (method), sharing one development process and set of standards with CRT-P, CRT-RMS, and other tests.

The payoff of this taxonomy is concrete:

- **Browse the way research thinks.** Overview dashboards group pages by role automatically; "every instrument measuring a given `construct`," or which `measure`s sit under a given `method`, are questions a single query can exhaust.
- **A minimum template for each role.** A construct page must have "definition, measurement, sources"; a measure page must have "construct & dimensions, reliability & validity, sample items"; a theory page must have "core claims." The AI fills pages out against these templates, so pages distilled from different papers share a comparable skeleton.
- **When in doubt, fall back.** Classification follows an explicit decision order; anything undecidable goes to `concept` — better to stay conservative than to over-infer for taxonomy's sake.

## Double Insurance: Origin & Verification

The real danger of AI-era knowledge management is not that the AI makes mistakes — it's that the AI sounds exactly the same when it's wrong as when it's right. The vault's countermeasure is two orthogonal fields that record "what kind of source" and "verification status" separately:

![The trust matrix](assets/fig-3-trust-matrix.png)

| | `status: needs-review` | `status: verified` |
|---|---|---|
| **`origin: solid`** | Knowledge the AI distilled from real materials, awaiting your eyes | Knowledge you have verified |
| **`origin: ai-generated`** | AI-generated, or the product of your discussions with the AI — handle with most care | AI-generated but verified by you |

&nbsp;

Three iron rules: every page the AI creates starts as `needs-review`; only after you have read it with your own eyes may it become `verified`; and AI-generated content can never be "laundered" into first-hand material through verification — `verified` changes the review status, never the origin.

## Workflows

![The three workflows](assets/fig-4-workflows.png)

| Workflow | You say | The AI does |
|---|---|---|
| **Ingest** | "Ingest this paper in `Inbox/`" | Reads the paper → confirms the proposed page list with you → archives the original into a `raw/` source package → writes a source summary page → distills concept pages → updates index and log |
| **Query** | "Based on the vault, how faking-resistant are CRTs?" | Locates relevant pages via the index → traces back to raw originals when needed → distinguishes verified from unverified content in its answer → never writes back unasked |
| **Lint** | "Run a lint / health check on the vault" | Scans the whole vault: frontmatter compliance, broken links, naming violations, missing template sections, orphan pages → produces a severity-graded report → fixes after your confirmation |

&nbsp;

Every destructive operation — batch renames, moves, deletions — must be listed first and executed only after your confirmation.

## Getting Started

### 1. Get the vault

Clone this repository and open the folder in [Obsidian](https://obsidian.md).

### 2. Enable the overview dashboards (optional)

The overview pages depend on the **Dataview** plugin: in Settings — Community plugins, turn off Restricted mode and install Dataview from the marketplace, enable **JavaScript queries** in its settings, and enable the CSS snippet `.obsidian/snippets/overview-cards.css`.

### 3. Connect an AI Agent

The vault is mostly plain Markdown — **any agent that can read and write a folder and follow SCHEMA will do**:

- **Option A · Inside Obsidian**: install the community plugin Claudian, fill in the CLI path of Claude Code / Codex etc., and chat with the agent in the sidebar panel.
- **Option B · In a terminal / agent**: start Claude Code / Codex etc. with this vault as the working directory; the root-level `CLAUDE.md` / `AGENTS.md` automatically routes the agent to SCHEMA.

### 4. Daily Use

1. **Collect**: drop materials into `Inbox/`; if the type is already clear, file them directly under the matching folder in `raw/`.
2. **Distill**: tell the AI "ingest this paper in Inbox/". It presents a list of pages to create or change, and acts only after your nod.
3. **Browse**: read pages in `wiki/`, browse by research role in `wiki/overview/`, view the knowledge network in Obsidian's graph view, and query the full catalog in `meta/index.md`.

## Acknowledgements

- The structure and rulebook concept of this vault come from [Andrej Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
- Thanks to [Claude Code](https://claude.com/claude-code) and [Codex](https://openai.com/codex): the rulebook was iterated with them, and the vault is maintained by them day to day — which is itself a validation of this vault's premise.

## License

Released under the [MIT License](LICENSE).

---
