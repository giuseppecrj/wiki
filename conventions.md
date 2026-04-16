# Conventions

Referenced by every other skill file. Read this before writing or modifying any wiki page.

## The golden rule

**This is not Wikipedia about the thing — it's about the thing's role in the subject's life.**

Every page should capture why the subject matters to this person: what they think about it, how it connects to their work and interests, what they learned from it. The wiki is a writer's tool, not a filing system. Write pages that a future version of the user would want to re-read, not pages that summarize what Google already knows.

You are a writer, not a filing clerk.

## Filenames

- **Kebab-case, no spaces.** `my-article-title.md`, not `My Article Title.md`.
- Derive the slug from the title: strip punctuation, lowercase, hyphens for spaces.
- Source pages: slug the article title. `wiki/sources/algorithms-as-analysts.md`
- Entity pages: slug the entity name. `wiki/entities/andrej-karpathy.md`
- Concept pages: slug the concept. `wiki/concepts/llm-wiki-pattern.md`
- Topic hubs: use the topic slug directly. `wiki/topics/tech.md`
- Daily notes: `wiki/daily/YYYY-MM-DD.md`

If two different things produce the same slug, suffix the source: `openclaw-overview.md`. The entity keeps the clean slug.

## Wikilinks

Obsidian resolves wikilinks by filename, not by frontmatter `title`. Always use the kebab-case filename slug.

### In frontmatter arrays

Bare slug, quoted:

```yaml
entities:
  - "[[andrej-karpathy]]"
  - "[[anthropic]]"
topics:
  - tech
  - personal
```

Topics are plain strings. Entities use wikilink syntax.

### In body text

Aliased for readability:

```markdown
[[andrej-karpathy|Andrej Karpathy]] formalized the [[llm-wiki-pattern|LLM Wiki Pattern]].
```

## Frontmatter

Every wiki page has YAML frontmatter. Required fields vary by page type, but all pages must have:

```yaml
---
type: source | entity | concept | topic | daily
title: "Human-Readable Title"
---
```

- `type` is always present.
- `title` is always present.
- Arrays use YAML list format, never inline.
- Dates are always `YYYY-MM-DD`.

## Tone and voice

If `SOUL.md` has a `## Voice` section, follow it — that is the user's defined writing voice for this wiki. If no voice section exists, use these defaults:

- Factual and direct.
- Write for the future reader.
- No emojis unless explicitly requested.
- No AI voice.
- Short sentences.
- Attribution over assertion.

The voice spec is captured during `/wiki init` and can be updated at any time. It ensures every page in the wiki sounds like the same person wrote it — because the wiki is an extension of the user's thinking, not a neutral database.

If the user expresses a view during a session, note it under a `## User's take` section on the relevant page.

## Dates

- Always `YYYY-MM-DD`
- `date_ingested`: when the wiki processed the source
- `date_created`: when the source was originally published, if known
- In `## History` sections: `YYYY-MM-DD: [what changed and why]`

## Images

- Reference locally downloaded images with `![[filename.ext]]`
- Images go in `raw/assets/`
- If the source has images but they are not downloaded, omit them

## The rewrite philosophy

### Rewrite, don't append

When a new source adds information about an entity or concept that already has a page:

1. Read the existing page first.
2. Rewrite the page to integrate the new information.
3. Resolve contradictions between new and old claims.
4. Synthesize. If a pattern emerges across multiple sources, create a new concept page.

If an ingest only creates new pages and touches nothing existing, it was not deep enough.

### Rewrite-first routing

Before creating any new page, ask:

1. Does this source mainly strengthen an existing source, entity, concept, or topic page?
2. Is the new page carrying a genuinely distinct idea, or just a bookmark shell?
3. Would the wiki become easier or harder to navigate if this became its own page?

Default to rewriting existing pages first. Create a new page only when the new page will become a stable retrieval target in future sessions.

### Anti-cramming

If you are adding a third paragraph about a sub-topic to an existing article, that sub-topic probably deserves its own page.

### Anti-thinning

If you cannot write at least 3 meaningful sentences about something, do not create the page yet.

Do not preserve raw bookmarks as thin source pages. If a saved item has no substance beyond a URL or title, fetch the real content first. If the real content is unavailable, either fold the item into a digest page or skip creating a standalone source page.

### Anti-slop checklist

Before finalizing any wiki write, check:

- **No duplicate slugs across page types.** If a source and entity would collide, the source gets the suffixed slug.
- **No bookmark shells.** A source page must capture the actual idea delta, not just preserve that the link existed.
- **No speculative entities or concepts.** If it does not cross the threshold, fold it into the source page instead.
- **No taxonomy drift.** Put the page in the smallest correct bucket: source, entity, concept, topic, or daily.
- **No unchanged understanding.** The ingest should leave the wiki smarter. If nothing existing was sharpened, rewritten, connected, or challenged, the pass was too shallow.
- **No hidden conflicts.** If a new source disagrees with the wiki, resolve the disagreement or flag it explicitly.

### The entity threshold

Create an entity page when:
- The entity appears meaningfully across 2 or more sources
- The entity is central to a single important source
- The user explicitly asks about it

## Bi-temporal fact tracking

When a fact changes over time, track both when it was true and when the wiki learned it, using the `## History` section on entity and concept pages.
