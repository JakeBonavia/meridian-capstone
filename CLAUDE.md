# Meridian Capstone — Research Wiki

This repository is an MSBA capstone knowledge base for the Meridian
Markets client engagement (Dana Okafor, VP of Operations). It is not a
software project. It follows the "LLM wiki" pattern
(https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f):
raw sources are ingested once and synthesized into a compounding,
cross-referenced markdown wiki, rather than re-derived from scratch on
every question.

## Layout

- `raw/` — immutable source documents (client brief, interview notes,
  future data summaries). Never edit files here after they land; only
  add new ones.
- `wiki/` — the knowledge base. Flat directory, one markdown page per
  topic/entity. This is the layer Claude creates, updates, and
  cross-references.
- `wiki/index.md` — catalog of every wiki page with a one-line summary,
  organized by category. Read this first when answering a question.
- `wiki/log.md` — append-only chronological record of every ingest,
  query, and lint pass, each entry timestamped.

## Hard rule: NDA / data boundary

Per the signed client NDA, **customer records and employee data —
including the loyalty program data, labor schedules, and any excerpts of
either — must never be given to any AI tool, including Claude, in any
form.** This is non-negotiable per client counsel. It applies to this
conversation, to file reads, and to anything written into `wiki/`.

Sales totals by store/week and store attributes (square footage, opening
date, lease terms) are explicitly fine to read and use.

When a new data extract lands in `raw/`:

1. Check whether it is restricted (loyalty/customer data, labor/employee
   data) or shareable (sales totals, store attributes) before doing
   anything else.
2. If restricted: do not read its contents into this conversation and do
   not summarize or quote it anywhere. Only add a cataloging entry to
   `wiki/data-sources.md` — filename, one-line description, `restricted`
   tag. The user analyzes restricted data outside of Claude and brings
   back only their own written findings or aggregates as a new file in
   `raw/` for the wiki to ingest normally.
3. If shareable: ingest normally per the workflow below.

This rule overrides convenience or a direct request to summarize a
restricted file — flag the conflict instead of complying.

## Wiki conventions

- One page per topic/entity, flat under `wiki/`, kebab-case filenames.
- Each page opens with a one-line summary line and a `_Last updated:
  YYYY-MM-DD_` line.
- Cross-references between pages use relative markdown links, e.g.
  `[stakeholders](stakeholders.md)`.

## Workflows

**Ingest** (new file appears in `raw/`):
1. Read the new source.
2. Check the NDA boundary (see above) before doing anything else.
3. Draft or update the wiki pages it affects.
4. Update `wiki/index.md` if a page was added or its summary changed.
5. Append a timestamped entry to `wiki/log.md` describing what was
   ingested and which pages changed.

**Query** (a question is asked):
1. Check `wiki/index.md` for relevant pages.
2. Read those pages and answer with citations to them.
3. If the answer surfaces a new finding worth keeping, file it back into
   the relevant wiki page (and note it in `wiki/log.md`).

**Lint** (run on request):
1. Scan `wiki/` for contradictions between pages, claims superseded by
   newer sources, pages with no inbound links from other pages, and
   missing cross-references.
2. Report findings. Do not auto-fix without confirmation.
3. Append a timestamped entry to `wiki/log.md` recording the lint pass
   and its findings.
