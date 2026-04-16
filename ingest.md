# Ingest — Process sources into the wiki

Run session startup (L0-L1) first. Read `conventions.md` and `pages.md` before writing any pages.

This file covers three operations:
- ingest all pending files from `raw/`
- ingest a specific URL or pasted text
- save conversation insights

## Ingest all pending files

### Step 1: Scan `raw/`

List all files in `raw/` recursively. Ignore:
- `.DS_Store`
- Image files in `raw/assets/`
- Engagement metadata in `raw/assets/`

If `raw/` is empty, tell the user there are no files to ingest.

### Step 2: Classify each source

Determine source type:
- Markdown article
- PDF
- Image
- Audio
- CSV or TSV
- Other text

### Step 3: Cluster by topic

Read enough metadata or opening content to assign likely topics. Process related sources together so topic hubs evolve coherently.

### Batch checkpoint protocol

For batches of 5 or more sources, process in groups of 5. After each group:

1. Run the anti-slop checklist from `conventions.md` against everything written in this group.
2. Verify at least one existing page was meaningfully rewritten (if the sources touched existing clusters).
3. Report progress: "Group N of M complete. X sources created, Y pages rewritten."
4. Fix any issues before proceeding to the next group.

This prevents quality drift during large ingests. Do not skip the checkpoint — the cost of catching a bad pattern after 5 sources is far lower than catching it after 20.

### Step 4: Route before writing

Before creating any page for a source, decide:
- which existing pages must be read and rewritten
- whether the source deserves a standalone source page or belongs in a digest
- whether any slug collision is likely
- whether the material crosses the entity/concept threshold or should stay folded into the source page

Do not start by creating pages. Start by deciding where the new information belongs.

### Step 5: Process each source

For each source, run the ingest flow below.

### Step 6: Clean up

After processing:
- Delete processed files from `raw/`
- Update touched topic hub overviews
- Rebuild affected sections of `index.md`

### Step 7: Report

Report:
- sources created
- pages rewritten
- contradictions resolved

If no existing pages were rewritten, flag that the ingest may not have been deep enough.

## Ingest a specific URL

1. Fetch the URL content.
2. Run the ingest flow on it.
3. Report as above.

## Ingest pasted text

1. Classify the text by content.
2. Run the ingest flow.
3. Report as above.

## Save conversation insights

Capture substantive decisions, ideas, or opinions from the current conversation and file them into the relevant wiki pages using the same conventions.

### Capturing decisions

When the user has made a decision during the conversation — chosen one approach over another, formed an opinion, changed their mind — capture it with structure, not just the conclusion:

- **What was decided** — the choice, stated clearly.
- **What alternatives were considered** — what was on the table.
- **Why** — the reasoning that led to this choice.
- **What it supersedes** — if this reverses or updates an earlier position, say so.

Write decisions to a `## Decisions` section on the relevant entity or concept page. Treat this section as append-only — add new entries at the top with a date, don't rewrite earlier entries. This preserves the reasoning trail so the wiki captures judgment, not just facts.

## The ingest flow

### 1. Read the source

Read the full content.

For special formats:
- YouTube: use available tooling to extract metadata and subtitles if possible
- Audio: transcribe with available transcription tooling if possible
- Images: extract visible text and describe what is shown
- PDFs: read directly

### 2. Extract

Identify:
- Entities
- Concepts
- Claims
- Quotes
- Topics

### 3. Route the information

Decide, in order:
- which existing pages need rewrites
- whether this should be a standalone source page or part of a digest
- which new entities or concepts actually cross threshold
- whether any source/entity slug collision needs a suffixed source slug

If the source is just a shell bookmark, do not create a thin standalone page.

### 4. Create the source page

Write `wiki/sources/<slug>.md` using the template in `pages.md`.

### 5. Rewrite existing pages

For every extracted entity and concept:
- Search for an existing page
- If found, read it fully and rewrite it to integrate the new information
- If not found, create a new page only if it meets the threshold in `conventions.md`
- If a cross-source pattern emerges, create a new concept page
- Check for conflicting claims and resolve or flag them

### 6. Post-write review pass

Before cleanup, review the ingest against this checklist:
- Did at least one existing page get meaningfully rewritten when the source touched an existing cluster?
- Did any new page get created that should really have been a rewrite or digest entry instead?
- Did any source page end up as a bookmark shell instead of a substantive synthesis?
- Did any source/entity/concept slug collide?
- Did the touched topic hub, `index.md`, and `log.md` all reflect the new state?

If the answer to any of these is yes, fix it before declaring the ingest complete.
