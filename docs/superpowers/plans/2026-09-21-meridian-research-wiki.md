# Meridian Research Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up the Meridian capstone research wiki — a `CLAUDE.md` schema plus seven `wiki/` pages — seeded from `raw/client-brief.md`, ready to support the Workshop 1 stakeholder interview.

**Architecture:** Plain markdown, no code, no tooling. `raw/` holds immutable sources (already has `client-brief.md`). `wiki/` is a flat directory of topic pages plus `index.md` (catalog) and `log.md` (append-only ingest history). `CLAUDE.md` at the repo root is the schema Claude auto-loads every session, encoding the NDA data boundary as a hard rule and the ingest/query/lint workflows.

**Tech Stack:** Markdown files in a git repo. No build, no dependencies, no test runner — verification is by `test -f` / `grep` checks against required content, not unit tests.

**Spec:** `docs/superpowers/specs/2026-09-21-meridian-research-wiki-design.md`

## Global Constraints

- **NDA hard rule** (from spec): customer records and employee data — loyalty program data, labor schedules, and any excerpts of either — must never be given to any AI tool, in any form. Sales totals by store/week and store attributes are explicitly shareable. This rule must appear verbatim in spirit in `CLAUDE.md` and must never be violated by any wiki page's content.
- **Wiki conventions** (from spec): one page per topic/entity, flat under `wiki/`, kebab-case filenames, each page opens with a one-line summary and a `_Last updated: YYYY-MM-DD_` line, cross-references use relative markdown links.
- **No restricted data exists yet** — every page in this plan is sourced only from `raw/client-brief.md`, which contains no actual customer or employee records, only descriptions of what those datasets are.
- Today's date for all "Last updated" lines and log entries: **2026-09-21**.

---

### Task 1: `CLAUDE.md` schema

**Files:**
- Create: `CLAUDE.md` (repo root)

**Interfaces:**
- Consumes: nothing (first file written).
- Produces: the schema document every later task's content must be consistent with (NDA rule wording, wiki conventions, ingest/query/lint workflow names).

- [ ] **Step 1: Write `CLAUDE.md`**

```markdown
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
```

- [ ] **Step 2: Verify the NDA rule and workflow names are present**

Run: `grep -c "never be given to any AI tool" CLAUDE.md && grep -c "^\*\*Ingest\*\*" CLAUDE.md && grep -c "^\*\*Query\*\*" CLAUDE.md && grep -c "^\*\*Lint\*\*" CLAUDE.md`
Expected: each command prints `1` (no errors, no zero counts).

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "Add wiki schema (CLAUDE.md) for Meridian capstone

Defines the karpathy LLM-wiki pattern, the NDA data boundary as a
hard rule, and ingest/query/lint workflows."
```

---

### Task 2: `wiki/business-overview.md`

**Files:**
- Create: `wiki/business-overview.md`

**Interfaces:**
- Consumes: facts from `raw/client-brief.md` (revenue, store count, employee count, growth history).
- Produces: `business-overview.md`, linked from Task 7's `index.md` under "Business" and cross-linked by `stakeholders.md`, `data-sources.md`, `assumptions.md`.

- [ ] **Step 1: Create the `wiki/` directory and write the file**

```bash
mkdir -p wiki
```

```markdown
# Business Overview

Meridian Markets is a 14-store specialty grocery chain across LA, Orange, and Ventura counties, competing on prepared foods, local sourcing, and a smaller footprint than national chains.

_Last updated: 2026-09-21_

## Key facts

- **Stores:** 14, across Los Angeles, Orange, and Ventura counties.
- **Revenue:** ~$78M/year.
- **Employees:** ~620.
- **Competitive positioning:** prepared foods, local sourcing, smaller footprint than national chains.

## Growth history

- Grew from 6 stores to 14 stores over 5 years (i.e., since roughly 2021).
- Growth mostly came from taking over leases from chains that pulled out of neighborhoods Meridian judged underserved.
- Growth "hasn't been even" — some stores took off immediately, others have been slower to find their footing (specifics not yet known — see [assumptions](assumptions.md)).

## Strategic context

- Leadership wants a clearer picture before the next round of expansion; decisions have historically been made "on instinct and a spreadsheet."
- The Pasadena site is considered the obvious next location, but leadership wants data to back that up before committing (see [assumptions](assumptions.md)).
- Stated goals: increase revenue, reduce operating costs, improve customer experience.

## Sources

- `raw/client-brief.md` (2026-08, from Dana Okafor)

## See also

- [stakeholders](stakeholders.md)
- [data-sources](data-sources.md)
- [assumptions](assumptions.md)
```

- [ ] **Step 2: Verify facts match the source brief**

Run: `grep -q "78M" raw/client-brief.md && grep -q "78M" wiki/business-overview.md && grep -q "fourteen" raw/client-brief.md && grep -q "^- \*\*Stores:\*\* 14" wiki/business-overview.md && echo OK`
Expected: prints `OK`.

- [ ] **Step 3: Commit**

```bash
git add wiki/business-overview.md
git commit -m "Add business-overview wiki page

Seeded from raw/client-brief.md: store count, revenue, growth history,
strategic context."
```

---

### Task 3: `wiki/stakeholders.md`

**Files:**
- Create: `wiki/stakeholders.md`

**Interfaces:**
- Consumes: people named in `raw/client-brief.md` (Dana Okafor, Marcus, Dana's assistant, leadership/board); today's date (2026-09-21) for the board-timeline check.
- Produces: `wiki/stakeholders.md`, linked from `index.md` under "People", cross-linked by `business-overview.md`, `data-sources.md`, `interview-questions.md`.

- [ ] **Step 1: Write the file**

```markdown
# Stakeholders

Who's involved in the Meridian engagement, their role, and how to reach them.

_Last updated: 2026-09-21_

## Dana Okafor — VP of Operations

- Primary contact for the engagement; wrote the client brief.
- Email is the best way to reach her.
- Travels Tuesdays and Wednesdays; slow to reply — silence is not a signal of a problem.
- Can answer analytics/business questions directly.

## Dana's assistant

- Can schedule time with Dana.
- Cannot answer questions about the analytics — don't route substantive questions here.

## Marcus — IT contact

- Will pull a data extract once the NDA is signed.
- Contact point for data access questions (POS, loyalty, labor, store attributes).

## Leadership / the board

- Leadership is the ultimate decision-maker on the next store location and on operational changes.
- The brief (dated August 2026) says the board meets "in three weeks" and Dana wants something to show them, even preliminary. As of this writing (2026-09-21) that puts the meeting in the past — see [assumptions](assumptions.md) for the timeline gap this raises.

## See also

- [business-overview](business-overview.md)
- [data-sources](data-sources.md)
- [interview-questions](interview-questions.md)
```

- [ ] **Step 2: Verify required people are present**

Run: `grep -q "Dana Okafor" wiki/stakeholders.md && grep -q "Marcus" wiki/stakeholders.md && grep -q "assistant" wiki/stakeholders.md && grep -q "board" wiki/stakeholders.md && echo OK`
Expected: prints `OK`.

- [ ] **Step 3: Commit**

```bash
git add wiki/stakeholders.md
git commit -m "Add stakeholders wiki page

Seeded from raw/client-brief.md: Dana Okafor, her assistant, Marcus
(IT), and leadership/board, with the board-timeline gap flagged."
```

---

### Task 4: `wiki/data-sources.md`

**Files:**
- Create: `wiki/data-sources.md`

**Interfaces:**
- Consumes: the four datasets named in `raw/client-brief.md` and the NDA rule from `CLAUDE.md` (Task 1).
- Produces: `wiki/data-sources.md`, linked from `index.md` under "Data", cross-linked by `business-overview.md`, `assumptions.md`. This is the page Task 1's ingest workflow points to for cataloging any future restricted data.

- [ ] **Step 1: Write the file**

```markdown
# Data Sources

What data Meridian has, and whether it's shareable with AI tools under the NDA.

_Last updated: 2026-09-21_

## Shareable (fine to use with AI tools per the brief)

| Dataset | Description | Notes |
|---|---|---|
| POS transactions | ~3 years of point-of-sale transaction history | Shareable **in aggregate** (sales totals by store/week); the brief doesn't clarify whether raw line-item transactions count as shareable — confirm with Dana. POS system was migrated last spring (2026); check comparability across the cutover. |
| Store attributes | Square footage, opening date, lease terms, per store | Explicitly named as shareable in the brief. |

## Restricted (must never enter any AI tool, per signed NDA — no exceptions)

| Dataset | Description | Notes |
|---|---|---|
| Loyalty program data | ~40,000 members, membership + purchase history | Restricted. Brief notes this data has "never really" been used for analysis — an opportunity, but must be analyzed outside of AI tools. |
| Labor scheduling / hours | Employee scheduling and hours data | Restricted. |

## Handling rule

Restricted datasets are cataloged here (name, description, restricted tag)
but their contents are never read into this conversation or into any wiki
page. The user analyzes restricted data outside of Claude and brings back
only their own written findings or aggregates as a new file in `raw/`.

## Access

- IT (Marcus) can pull an extract once the NDA is signed — see [stakeholders](stakeholders.md).

## See also

- [business-overview](business-overview.md)
- [assumptions](assumptions.md)
```

- [ ] **Step 2: Verify all four datasets and the restricted tags are present**

Run: `grep -q "POS transactions" wiki/data-sources.md && grep -q "Store attributes" wiki/data-sources.md && grep -q "Loyalty program data" wiki/data-sources.md && grep -q "Labor scheduling" wiki/data-sources.md && grep -c "Restricted\." wiki/data-sources.md`
Expected: all `grep -q` succeed silently, final command prints `2` (loyalty row + labor row each carry a "Restricted." note).

- [ ] **Step 3: Commit**

```bash
git add wiki/data-sources.md
git commit -m "Add data-sources wiki page

Catalogs POS, loyalty, labor, and store-attribute datasets from
raw/client-brief.md with shareable/restricted tags per the NDA."
```

---

### Task 5: `wiki/assumptions.md`

**Files:**
- Create: `wiki/assumptions.md`

**Interfaces:**
- Consumes: `business-overview.md` (Task 2), `data-sources.md` (Task 4) content; today's date (2026-09-21) for the board-timeline check also noted in `stakeholders.md` (Task 3).
- Produces: `wiki/assumptions.md`, linked from `index.md` under "Research". Every question in Task 6's `interview-questions.md` must cite one of this page's bullets.

- [ ] **Step 1: Write the file**

```markdown
# Open Assumptions & Gaps

Claims from the client brief that are unvalidated, ambiguous, or need
confirmation before we build on them. Each one should map to a question
in [interview-questions](interview-questions.md).

_Last updated: 2026-09-21_

## Expansion

- **"Pasadena is the obvious next site"** — stated as the leadership's leaning, but the brief explicitly says they want data to back it up before committing. Unvalidated: what criteria would confirm or rule out Pasadena? What alternative sites, if any, are on the table?
- Expansion decisions have historically been made "on instinct and a spreadsheet" — what's in that spreadsheet today, and who owns it?

## Performance

- **"Growth has been uneven"** — some stores took off immediately, others have been slower. Unknown: which stores, which categories, by how much, and over what time window?
- Success is framed as three goals at once — increase revenue, reduce operating costs, improve customer experience — with no stated priority order. If they conflict in a specific decision, which wins?

## Data

- POS system was migrated last spring (2026) — unclear whether the ~3 years of POS history is continuous/comparable across that cutover, or whether pre-migration and post-migration data differ in structure or reliability.
- The brief doesn't specify whether raw POS transaction-level data counts as "shareable" (only "sales totals by store and week" are named explicitly) — confirm the exact shareable granularity.
- Loyalty program data (~40K members) has "never really" been analyzed — no stated hypothesis yet for what it should reveal.

## Timeline

- The brief is dated August 2026 and says the board meets "in three weeks" — that would land in early-to-mid September 2026, which appears to have already passed as of today (2026-09-21). The board-readout timeline needs reconfirming: has that meeting already happened, and if so, what came out of it? Is there a new target date?
- Overall engagement timeline is "eight weeks or so" from the brief — also worth confirming against today's date.

## See also

- [business-overview](business-overview.md)
- [data-sources](data-sources.md)
- [interview-questions](interview-questions.md)
```

- [ ] **Step 2: Verify all four gap categories are present**

Run: `grep -q "^## Expansion" wiki/assumptions.md && grep -q "^## Performance" wiki/assumptions.md && grep -q "^## Data" wiki/assumptions.md && grep -q "^## Timeline" wiki/assumptions.md && echo OK`
Expected: prints `OK`.

- [ ] **Step 3: Commit**

```bash
git add wiki/assumptions.md
git commit -m "Add assumptions wiki page

Lists unvalidated claims and gaps from raw/client-brief.md across
expansion, performance, data, and timeline, each feeding an interview
question."
```

---

### Task 6: `wiki/interview-questions.md`

**Files:**
- Create: `wiki/interview-questions.md`

**Interfaces:**
- Consumes: every bullet in `wiki/assumptions.md` (Task 5) and the stakeholder list in `wiki/stakeholders.md` (Task 3) — this is the project's primary deliverable.
- Produces: `wiki/interview-questions.md`, linked from `index.md` under "Research".

- [ ] **Step 1: Write the file**

```markdown
# Interview Questions — Dana Okafor

Prioritized questions for the Workshop 1 stakeholder interview. Each
question is grouped by theme and cites the assumption/gap it resolves in
[assumptions](assumptions.md).

_Last updated: 2026-09-21_

## Expansion decision criteria

1. What specific data or criteria would confirm — or rule out — Pasadena as the next site? *(resolves: "Pasadena is the obvious next site")*
2. Are there other candidate sites being considered alongside Pasadena, even informally?
3. Walk me through how the last one or two site decisions were actually made — what was in "the spreadsheet," and who was involved?

## Data access & definitions

4. Is raw, transaction-level POS data shareable with our analysis tools, or only aggregated sales totals by store/week as the brief states? *(resolves: shareable granularity gap)*
5. Does the ~3 years of POS history span the system migration last spring? Should we treat pre- and post-migration data differently, or is it fully comparable?
6. For the loyalty program data — since it hasn't been analyzed before, is there a specific question you'd want it to answer first (e.g., repeat-visit patterns, basket size, churn)?

## Store & category performance

7. When you say growth has been uneven, which stores or categories come to mind first as strong performers, and which as underperformers?
8. Is there a minimum performance bar a store needs to hit before it's considered "found its footing," or is this purely relative across the 14 stores?

## Success metrics & priorities

9. Of increasing revenue, reducing operating costs, and improving customer experience — if a decision forces a trade-off between them, which takes priority?
10. What would "success" for this dashboard look like concretely to you — a specific decision it should make easier, or a specific question it should answer?

## Timeline & logistics

11. The brief mentions the board meets "in three weeks" from August — has that meeting already happened, and if so, what came out of it? *(resolves: board timeline gap)*
12. Is the eight-week engagement timeline still accurate, or has it shifted?
13. Beyond you, is there anyone else on the Meridian side we should loop in for data or context questions, given your travel schedule?

## See also

- [assumptions](assumptions.md)
- [stakeholders](stakeholders.md)
```

- [ ] **Step 2: Verify all five themes and 13 numbered questions are present**

Run: `grep -cE "^[0-9]+\." wiki/interview-questions.md && grep -q "## Expansion decision criteria" wiki/interview-questions.md && grep -q "## Data access & definitions" wiki/interview-questions.md && grep -q "## Store & category performance" wiki/interview-questions.md && grep -q "## Success metrics & priorities" wiki/interview-questions.md && grep -q "## Timeline & logistics" wiki/interview-questions.md`
Expected: first command prints `13`, remaining commands succeed silently.

- [ ] **Step 3: Commit**

```bash
git add wiki/interview-questions.md
git commit -m "Add interview-questions wiki page

The primary deliverable: 13 prioritized questions for Dana Okafor,
grouped by theme, each tied back to a gap in assumptions.md."
```

---

### Task 7: `wiki/index.md` and `wiki/log.md`

**Files:**
- Create: `wiki/index.md`
- Create: `wiki/log.md`

**Interfaces:**
- Consumes: the five pages created in Tasks 2-6 (needs their filenames and one-line summaries).
- Produces: `wiki/index.md` (the catalog every future query workflow reads first) and `wiki/log.md` (the append-only history every future ingest appends to).

- [ ] **Step 1: Write `wiki/index.md`**

```markdown
# Wiki Index

Catalog of every page in this wiki, organized by category. Check here
first before answering a question.

_Last updated: 2026-09-21_

## Business

- [business-overview](business-overview.md) — Meridian Markets facts, growth history, strategic context.

## People

- [stakeholders](stakeholders.md) — who's involved in the engagement, roles, how to reach them.

## Data

- [data-sources](data-sources.md) — what data Meridian has, and what's shareable vs. restricted under the NDA.

## Research

- [assumptions](assumptions.md) — open gaps and unvalidated claims from the client brief.
- [interview-questions](interview-questions.md) — prioritized question list for the Dana Okafor interview.
```

- [ ] **Step 2: Write `wiki/log.md`**

```markdown
# Log

Append-only chronological record of ingests, queries, and lint passes.
Newest entries at the bottom.

## 2026-09-21 — Initial ingest: client brief

- Source: `raw/client-brief.md` (Dana Okafor, August 2026).
- Created: `business-overview.md`, `stakeholders.md`, `data-sources.md`, `assumptions.md`, `interview-questions.md`, `index.md`.
- NDA check: brief names loyalty program and labor scheduling data as restricted; no restricted data was available to ingest yet (brief itself contains no customer/employee records), so no restricted content was read or summarized.
- Noted in `assumptions.md`: the "board meets in three weeks" timeline from the August 2026 brief appears to have already elapsed as of this ingest date — flagged for confirmation in the interview.
```

- [ ] **Step 3: Verify every wiki page is cataloged in `index.md` and every linked file actually exists**

Run:
```bash
for f in business-overview stakeholders data-sources assumptions interview-questions; do
  grep -q "($f.md)" wiki/index.md && test -f "wiki/$f.md" && echo "OK: $f" || echo "MISSING: $f"
done
```
Expected: five lines, each `OK: <name>`, no `MISSING` lines.

- [ ] **Step 4: Commit**

```bash
git add wiki/index.md wiki/log.md
git commit -m "Add wiki index and log

index.md catalogs all six wiki pages by category; log.md records the
initial ingest of raw/client-brief.md."
```

---

## Self-Review Notes

- **Spec coverage:** repository layout (Task 1 + mkdir in Task 2), CLAUDE.md schema contents including NDA rule and all three workflows (Task 1), all six initial pages with their specified content (Tasks 2-6 + index in Task 7), log seeding (Task 7) — all spec sections have a corresponding task.
- **Placeholder scan:** no TBD/TODO markers; every step contains literal file content or an exact runnable command.
- **Type/naming consistency:** filenames (`business-overview.md`, `stakeholders.md`, `data-sources.md`, `assumptions.md`, `interview-questions.md`, `index.md`, `log.md`) match exactly across every cross-reference in every task. Workflow names (Ingest/Query/Lint) match between `CLAUDE.md` (Task 1) and their usage in `log.md` entries (Task 7).
- **Out of scope confirmed:** no task ingests restricted data or builds tooling/automation, matching the spec's "Out of scope" section.

---

## Review Notes

Notes from reviewing the AI's output against the source material, logged per unit of work across the engagement (not all of these map 1:1 to the numbered tasks above, since Tasks 2 and 3 happened after this plan's initial build).

### Task 1 — Initial wiki build from client brief

- **Asked / got back:** Asked Claude to build the wiki seeded from `raw/client-brief.md`. It created 6 pages (`business-overview.md`, `stakeholders.md`, `data-sources.md`, `assumptions.md`, `interview-questions.md`, `index.md`) plus `CLAUDE.md`.
- **Checked:** Verified the NDA boundary was stated correctly in `CLAUDE.md` (restricted vs. shareable datasets, the "never given to any AI tool" rule) and scanned every new wiki page for any restricted (loyalty/labor) data.
- **Outcome:** Accepted — pages matched the brief's facts and the NDA rule was implemented correctly, with no restricted data present anywhere.

### Task 2 — ICSC source ingest

- **Asked / got back:** Asked Claude to ingest the ICSC grocery article (`raw/icsc-grocery-formats-2026-04-24.md`). It created `industry-context.md` and updated 4 existing pages with new sections, plus added 2 new interview questions.
- **Checked:** Opened the original ICSC article alongside `wiki/industry-context.md` and spot-checked the claim that Trader Joe's grew 3% YoY.
- **Outcome:** Accepted — the article confirmed the figure, so the sourced claim was accurate.

### Task 3 — Specialty grocer locations query

- **Asked / got back:** Asked what the ingested sources say about where specialty grocers are opening. Claude answered with 4 sourced claims and explicitly labeled its own synthesis/conclusion as unsourced.
- **Checked:** Traced the Redlands Whole Foods claim back to the ICSC article to confirm it wasn't fabricated.
- **Outcome:** Accepted — claims were properly cited and the unsourced gap was honestly flagged rather than presented as fact.
