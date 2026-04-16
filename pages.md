# Page Formats

Every wiki page follows one of these templates. Read `conventions.md` for naming, linking, and tone rules.

## Overview page — `wiki/overview.md`

The overview is the wiki's thesis statement — a high-level synthesis of what the wiki contains and what it means. It is not a table of contents (that's `index.md`) but a narrative connecting the major themes.

**When to update the overview:**
- After any ingest that introduces a new topic area or shifts the high-level picture
- After `/wiki synthesize` surfaces a cross-topic pattern that changes the wiki's direction
- After the user explicitly requests it

If neither condition is met during an ingest, leave the overview alone. Don't rewrite it just because new sources were added — only when the shape of the wiki's knowledge has meaningfully changed.

## Source page — `wiki/sources/<slug>.md`

```markdown
---
type: source
title: "Article Title"
url: https://...
date_ingested: YYYY-MM-DD
date_created: YYYY-MM-DD
topics:
  - tech
entities:
  - "[[entity-slug]]"
---

## Summary
2-4 paragraph synthesis.

## Key points
- Important claims or data points.

## Connections
- Related wiki pages and why they matter.

## Quotes
Notable passages worth preserving verbatim.
```

## Entity page — `wiki/entities/<slug>.md`

```markdown
---
type: entity
entity_type: person | company | product | project
title: "Entity Name"
topics:
  - tech
---

## About
Who or what this is.

## Appearances
How this entity shows up across sources.

## History
- YYYY-MM-DD: [what changed and why, with source link]

## Connections
Related entities and concepts.
```

## Concept page — `wiki/concepts/<slug>.md`

```markdown
---
type: concept
title: "Concept Name"
topics:
  - crypto
  - tech
---

## Definition
Clear definition.

## Why it matters
Relevance to the wiki.

## Sources
- [[source-slug|Source Title]] — what the source adds

## Related
Related concepts and entities.
```

## Topic hub page — `wiki/topics/<topic>.md`

```markdown
---
type: topic
title: "Topic Name"
---

## Overview
Current synthesis of the topic.

## Key entities
Important entities with brief context.

## Key concepts
Important concepts with brief context.

## Sources
- [[source-slug|Source Title]] — one-line summary (YYYY-MM-DD)

## Open questions
Questions the wiki raises but cannot yet resolve.
```
