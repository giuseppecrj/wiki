---
name: wiki
version: 0.1.0
description: "LLM-maintained personal knowledge wiki for Obsidian and markdown vaults. Ingest sources, query knowledge, detect cross-source patterns, audit health, create daily notes, and build a compounding second brain. Install via Claude Code or pkts."
license: MIT
compatibility: |
  Runs in any Agent Skills-compatible runtime (Claude Code, Codex, Cursor, Gemini).
  Operates on a local markdown directory; wikilinks render natively in Obsidian.
  Optional integrations used when available: URL fetching, YouTube metadata/subtitles, audio transcription, Google Calendar.
metadata:
  category: knowledge-management
  tags: [wiki, obsidian, markdown, second-brain, notes, research, knowledge-base]
  author: pkts.run
  homepage: https://pkts.run/wiki
---

# Wiki

Use this skill when the user wants to operate on a personal knowledge wiki stored in the current workspace.

Typical triggers:
- "ingest", "process", "add to wiki" — source processing
- "what do we know about", "query", "search the wiki" — knowledge retrieval
- "wiki health", "audit", "broken links" — maintenance
- "synthesize", "patterns", "what's emerging" — pattern detection
- "autoresearch", "deep research", "research this" — parallel-exploration research on a specific question
- "daily note", "today" — daily notes
- "challenge", "red-team", "pressure-test" — idea testing
- "start a wiki", "init", "bootstrap" — new wiki setup
- "save this", "remember this" — conversation capture

The human curates sources and directs exploration. The agent does all writing, cross-referencing, and maintenance.

## Pre-flight check

Before any operation except `init`, verify the wiki is initialized:

1. Check if `index.md` exists in the working directory.
2. If it does not exist, check if `SOUL.md` exists. If neither exists, tell the user: *"This directory doesn't have a wiki yet. Run `/wiki init` to set one up."* Then stop.

This prevents silent failures for new users who install the skill and try to ingest before bootstrapping.

## Architecture

Three layers:

1. **Raw sources** (`raw/`) — immutable input. The agent reads, then deletes after ingest.
2. **The wiki** (`wiki/`) — agent-owned generated pages. The human reads; the agent writes.
3. **The schema** (`CLAUDE.md`) — tells the agent how the wiki is structured.

Supporting files at root: `index.md` (content index), `log.md` (append-only log), `SOUL.md` (identity), `CRITICAL_FACTS.md` (always-load context), `PINNED.md` (session context, optional).

Fixed taxonomy — four page types plus daily notes:
- `wiki/sources/` — one page per ingested source
- `wiki/entities/` — people, companies, products, projects
- `wiki/concepts/` — ideas, frameworks, themes
- `wiki/topics/` — hub pages per major topic area
- `wiki/daily/` — daily notes (`YYYY-MM-DD.md`)

## Session startup — progressive context loading

Always load L0-L1 at the start of any wiki operation. Load deeper levels only when needed.

**L0 — Identity (~170 tokens)**
Read if they exist: `SOUL.md`, `CRITICAL_FACTS.md`, `PINNED.md`

**L1 — Navigation (~1-2K tokens)**
Read: `index.md`, `log.md` (last 10 entries only)

**L2 — Current state (~2-5K tokens)**
Read: today's daily note (if it exists), recent log activity. Load when the user asks about current state or wants the daily note.

**L3 — Deep context (on demand)**
Read full wiki pages only for operations that require them: query, challenge, synthesize, health/reconcile, or ingest when rewriting existing pages.

After loading L0-L1, you know who the user is and what's in the wiki. That's enough to route any operation.

## Core philosophy

Read `conventions.md` for the full rules. The short version:

1. **Writer, not filing clerk** — every page captures why the subject matters to this person, not neutral encyclopedia prose.
2. **Rewrite, don't append** — every ingest makes existing pages smarter, not just bigger.
3. **Anti-cramming** — split when a sub-topic deserves its own page.
4. **Anti-thinning** — every page must have substance. No stubs.
5. **Append-only logging** — never edit `log.md` entries. Only append.
6. **Progressive disclosure** — load identity first, then index, then deep context on demand.
7. **File system over RAG** — `index.md` routing plus file navigation beats embedding-based retrieval.
8. **Bi-temporal facts** — track when something was true and when the wiki learned it.

## Operation routing

When the user asks for one of these operations, read the referenced file and execute that workflow:

| Intent | Action |
|--------|--------|
| Ingest all pending files from `raw/` | Read `ingest.md`, then execute the ingest workflow |
| Ingest a specific URL or pasted text | Read `ingest.md`, then execute the single-source workflow |
| Save conversation insights | Read `ingest.md`, then execute the save workflow |
| Bootstrap a new wiki | Read `init.md`, then execute |
| Query the wiki | Read `query.md`, then execute |
| Audit wiki health | Read `health.md`, then execute |
| Reconcile contradictions | Read `health.md`, then execute the reconcile workflow |
| Synthesize patterns | Read `synthesize.md`, then execute |
| Autoresearch a specific question via parallel exploration | Read `autoresearch.md`, then execute |
| Challenge an idea against wiki history | Read `challenge.md`, then execute |
| Create or update today's daily note | Read `daily.md`, then execute |

Before executing any operation (except init), run the pre-flight check, then the session startup protocol (L0-L1). The operation-specific file will specify if deeper loading is needed.

## References

These files define the conventions and page formats. Read them when writing or modifying any wiki page:

- `conventions.md` — naming, linking, formatting, tone, golden rule, anti-patterns
- `pages.md` — templates for all page types
