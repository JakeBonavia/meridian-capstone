# Meridian Capstone Research Wiki — Design

Date: 2026-09-21
Status: Approved by user, pending implementation

## Purpose

Build a persistent, LLM-maintained research wiki for the Meridian Markets
MSBA capstone engagement, following Andrej Karpathy's "LLM wiki" pattern
(https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The
wiki compounds knowledge across all four workshops of the engagement,
starting with prep for the Workshop 1 stakeholder interview with Dana
Okafor (VP of Operations).

The wiki replaces re-deriving context from raw sources on every question:
sources are ingested once, synthesized into cross-referenced markdown
pages, and later queries read the wiki rather than re-reading raw sources.

## Scope

- **Lifespan**: full capstone knowledge base, not a one-off interview-prep
  artifact. Persists and grows across all four workshops.
- **Audience**: solo — the user is the only contributor to this repo.
- **Immediate deliverable**: a prioritized, rationale-backed question list
  for the Dana Okafor interview, plus supporting pages (stakeholders,
  assumptions) that make the rationale visible.

## Hard constraint: NDA / data boundary

The client brief (`raw/client-brief.md`) states that customer records and
employee data — loyalty program data, labor schedules, and any excerpts of
either — must never be given to any AI tool, including this one. This is
non-negotiable per client counsel. Sales totals by store/week and store
attributes (square footage, opening date, lease terms) are explicitly fine
to use with AI tools.

This constraint is binding on the wiki's design, not just a note in it:

- Restricted datasets (loyalty program, labor scheduling) are **cataloged**
  in `wiki/data-sources.md` — filename, one-line description, and a
  `restricted` tag — but their contents are never read into this
  conversation, never summarized, and never quoted into any wiki page.
- The user analyzes restricted data outside of Claude (e.g. Excel/Python)
  and brings only their own written findings or aggregates back as a new
  source in `raw/` for the wiki to ingest.
- This rule is stated explicitly in `CLAUDE.md` as a hard rule, not a
  default the assistant can override based on convenience.

## Repository layout

```
meridian-capstone/
├── CLAUDE.md              # schema: wiki rules & workflows, auto-loaded every session
├── README.md
├── raw/                   # immutable sources — never edited, only added to
│   └── client-brief.md
└── wiki/                  # flat, LLM-maintained, compounding knowledge base
    ├── index.md           # catalog of all pages, one-line summaries, by category
    ├── log.md             # append-only: every ingest/query/lint, timestamped
    ├── stakeholders.md
    ├── business-overview.md
    ├── data-sources.md
    ├── assumptions.md
    └── interview-questions.md
```

- `raw/` holds every source as received — the client brief now; later,
  interview notes, additional briefs, and any *shareable* data summaries.
  Files here are never rewritten by the assistant.
- `wiki/` is flat (no subfolders by type). At capstone scale (roughly
  10-20 pages across four workshops), `index.md` carries the organization
  instead of folder structure, matching the gist's own example and
  avoiding premature categorization decisions.
- Pages are organized **by topic/entity, not by workshop** — e.g.
  `business-overview.md` gets updated in place as Workshop 2, 3, and 4
  add information, rather than being duplicated into per-workshop
  folders. `log.md` provides the chronological/workshop-relative view
  that topic-oriented pages don't.

## `CLAUDE.md` schema contents

`CLAUDE.md` at the repo root is the schema document — auto-loaded by
Claude Code every session, so wiki rules are always in context with no
extra step. It defines:

1. **Purpose statement**: this repo is an MSBA capstone knowledge base
   for the Meridian Markets engagement, built as a compounding wiki, not
   a software project.
2. **The NDA/data boundary**, stated in full and marked as a hard rule
   (see above).
3. **Wiki conventions**:
   - One page per topic/entity, flat under `wiki/`, kebab-case filenames.
   - Each page opens with a one-line summary and a "Last updated" line.
   - Cross-references use relative markdown links between pages.
4. **Workflows**, adapted from the gist to this project:
   - **Ingest**: new file lands in `raw/` → read it → check the NDA
     boundary first → draft/update affected wiki pages → update
     `index.md` → append a timestamped entry to `log.md`.
   - **Query**: a question comes in → check `index.md` → read relevant
     pages → answer with citations to wiki pages → optionally file new
     findings back as page updates.
   - **Lint**: on request, scan for contradictions between pages, stale
     claims superseded by newer sources, orphan pages, and missing
     cross-references; report findings and fix only with confirmation.

## Initial page content (seeded from `raw/client-brief.md` only)

No restricted data exists yet, so all initial content is sourced from the
client brief:

- **`business-overview.md`** — Meridian facts: 14 stores across LA/Orange/
  Ventura counties, ~$78M annual revenue, ~620 employees, grew from 6 to
  14 stores in 5 years by taking over leases from chains that exited
  neighborhoods deemed underserved, competes on prepared foods, local
  sourcing, and smaller footprint than national chains.
- **`stakeholders.md`** — Dana Okafor (VP Operations, primary contact,
  travels Tue/Wed, slow to reply), Marcus (IT contact, provides data
  extract once NDA is signed), Dana's assistant (scheduling only, cannot
  answer analytics questions), leadership/board (decision-makers; board
  meets in 3 weeks and wants a preliminary readout).
- **`data-sources.md`** — the four datasets named in the brief, each
  tagged shareable or restricted: POS transactions (~3 years, shareable
  in aggregate), loyalty program (~40K members, **restricted**), labor
  scheduling/hours (**restricted**), store attributes — sq ft, opening
  date, lease terms (shareable).
- **`assumptions.md`** — open gaps to probe in the interview: "Pasadena
  is the obvious next site" (unvalidated by data), "growth has been
  uneven" (which stores/categories, by how much?), expansion decisions
  currently made "on instinct and a spreadsheet," loyalty data "never
  really used," POS migration last spring (data continuity/comparability
  across the cutover), success framed as revenue + cost + customer
  experience simultaneously (any stated priority order?).
- **`interview-questions.md`** — the deliverable: a prioritized question
  list for Dana, grouped by theme (expansion decision criteria, data
  access & definitions, customer & loyalty, success metrics & priorities,
  timeline & board-readout logistics). Each question is tied back to the
  specific assumption or gap in `assumptions.md` it's meant to resolve.
- **`index.md`** and **`log.md`** seeded per the schema, with one `log.md`
  entry recording this initial ingest of the client brief.

## Out of scope

- No handling of restricted data ingestion is specified beyond the
  cataloging rule above — that workflow is intentionally deferred until
  the user has actual restricted extracts and a concrete process to
  propose.
- No tooling/automation (scripts, CI, search indexing) — the wiki is
  plain markdown maintained conversationally, per the gist's pattern.
- No workshop-specific structure beyond what's listed; later workshops'
  needs (e.g. a dashboard-design page, a findings page) will extend this
  same flat `wiki/` structure when they arise, not restructure it.
