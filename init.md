# Init — Bootstrap a new wiki

This is the only operation that skips the pre-flight check — init *creates* the wiki that other operations require.

If `CLAUDE.md` already exists, warn before overwriting it. If `index.md` already exists, regenerate it. If `log.md` already exists, append only.

## Step 1: Generate the directory structure

Create the scaffold immediately before asking the user anything:

```text
raw/
raw/assets/
wiki/
wiki/sources/
wiki/entities/
wiki/concepts/
wiki/topics/
wiki/daily/
```

This command should not stop after scaffold creation. Once the folders exist, continue directly into the interview step in the same `init` flow.

## Step 2: Ask questions

Collect enough information to define the wiki schema:

1. **What the wiki is about** — what domains, interests, or work does it cover?
2. **Who the user is** — role, background, what they're working on.
3. **What the topic areas are** — the major categories for organizing knowledge. Suggest 3-7 to start.
4. **What tone the wiki should use** — formal vs. casual, dense vs. conversational.
5. **How should the wiki sound?** — ask the user to describe their preferred writing voice. Examples to prompt them: "Short punchy sentences or longer flowing prose? Technical or accessible? Confident or measured?" This becomes the `## Voice` section in `SOUL.md`.

If useful, also ask about timezone, current role, bootstrap content, and preferred form of address.

If the user skips the voice question, generate a sensible default from their other answers (e.g., a technical user gets "factual, direct, no hedging"; a creative user gets "vivid, opinionated, concrete").

## Step 3: Generate root docs

Create or update the following files using the user's answers and the conventions defined in this skill.

### `SOUL.md`

The user's identity document. Include:
- `## Role` — who they are, what they do
- `## How I think` — working style, depth preferences
- `## Values` — what matters to them
- `## Current focus areas` — what they're working on now
- `## Voice` — their writing voice spec from question 5. This is referenced by `conventions.md` for all wiki writing.

### `CRITICAL_FACTS.md`

A short file (~100 tokens) with facts that are always true and always needed. Examples: employer, timezone, primary tools. Keep it minimal — this is loaded at session start.

### `CLAUDE.md`

The wiki schema document. Use this template, filling in the user's topic areas and any customizations:

```markdown
# [Wiki Name] — Agent Schema

This file describes how the coding agent operates on this wiki. Read this file at the start of every session.

## What this is

A personal knowledge wiki for [user] — a persistent, compounding knowledge base covering [their interests]. The wiki is LLM-maintained. [User] curates sources and directs exploration; the agent does all the writing, cross-referencing, and maintenance.

## Directory structure

\```
/
├── CLAUDE.md              ← wiki schema (this file)
├── SOUL.md                ← identity, values, thinking style, voice
├── CRITICAL_FACTS.md      ← always-needed context
├── PINNED.md              ← session context pinning (optional)
├── index.md               ← content index, updated on every operation
├── log.md                 ← append-only chronological log
├── raw/                   ← source documents (deleted after ingest)
│   └── assets/            ← locally downloaded images and files
└── wiki/                  ← all LLM-generated pages
    ├── overview.md        ← high-level synthesis
    ├── sources/           ← one page per ingested source
    ├── topics/            ← hub pages for major topic areas
    ├── entities/          ← people, companies, products, projects
    ├── concepts/          ← ideas, frameworks, themes
    └── daily/             ← daily notes
\```

**Rules:**
- `raw/` is an inbox. Add files here; the agent deletes them after ingest.
- Everything in `wiki/` is agent-owned. You read it; the agent writes it.
- `index.md` and `log.md` are in the root — always kept updated.
- `SOUL.md` and `CRITICAL_FACTS.md` are co-owned — agent writes, you edit freely.

## Topic areas

| Slug | Description |
| ---- | ----------- |
[filled from user's answers]

A source or entity can belong to multiple topics.

## Commands

All operations are handled by the `/wiki` skill:

| Command | What it does |
| ------- | ------------ |
| `/wiki ingest` | Scan `raw/` for new files, process them all |
| `/wiki ingest [url]` | Fetch and process a specific URL |
| `/wiki query [question]` | Answer a question from the wiki with citations |
| `/wiki health` | Audit: broken links, contradictions, orphans, gaps |
| `/wiki synthesize` | Surface unnamed patterns across recent sources |
| `/wiki challenge [idea]` | Red-team an idea against wiki history |
| `/wiki daily` | Create or update today's daily note |
| `/wiki save` | Save conversation insights to the wiki |
| `/wiki reconcile [topic]` | Resolve contradictions |

## Session startup

At the start of every session:
1. Read `CLAUDE.md` (this file)
2. Read `SOUL.md` — who you are, values, voice
3. Read `CRITICAL_FACTS.md` — always-needed context
4. Read `PINNED.md` if it exists — session context
5. Read `log.md` (last 10 entries) — what happened recently
6. Read `index.md` — what's in the wiki
7. Ask what to work on

## Evolution

This schema should evolve. If a convention isn't working, change it. If you want a new topic area, add it to the table and create the hub page.
```

### `index.md`

Start with the topic areas as section headers, empty lists under each. The index will populate as sources are ingested.

### `log.md`

Start with a single entry:

```
YYYY-MM-DD — Wiki initialized. Topics: [list]. Ready for first ingest.
```

### Topic hub pages

Create one page per topic area under `wiki/topics/` using the template in `pages.md`. Each hub starts with a brief overview and empty sections for entities, concepts, sources, and open questions.

### `wiki/overview.md`

Create a brief overview page synthesizing the wiki's scope and the user's interests. This page evolves over time as the wiki grows.

## Step 4: Report

Tell the user:
- What was created
- How to add their first source (drop a file in `raw/` and run `/wiki ingest`, or paste a URL with `/wiki ingest [url]`)
- That the wiki will compound — every ingest makes existing pages smarter, not just bigger
