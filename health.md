# Health — Audit and repair the wiki

Run session startup (L0-L1) first. This operation reads broadly across the wiki.

This file covers two operations:
- wiki health audit
- contradiction reconciliation

## Health audit

### Step 1: Scan the wiki

Read `index.md` for the full catalog. Then inspect for:

- Broken wikilinks
- Ambiguous links or slug collisions
- Contradictions between entity and concept pages
- Orphan pages with zero inbound links
- Stale claims on fast-moving topics
- Concept gaps where a term appears repeatedly without a dedicated page
- Missing cross-references

### Step 2: Merge and classify

Group findings by severity:

- **Critical**: broken links, contradictions, ambiguous slugs
- **Warning**: orphans, stale claims, concept gaps, missing cross-references
- **Info**: anti-cramming candidates, thin pages, empty sections

### Step 3: Auto-fix safe issues

Fix these without asking:
- Add missing wikilinks where the target exists and the reference is unambiguous
- Create concept pages for strong gaps when there is enough context to make them useful
- Add clear cross-references between related pages

### Step 4: Flag destructive fixes

Ask before:
- Resolving contradictions where judgment is required
- Archiving or merging pages
- Renaming files to resolve slug collisions
- Deleting orphan pages

### Step 5: Report

Present a severity-grouped report and append a health entry to `log.md`.

## Reconcile contradictions

### Step 1: Find contradictions

If the user scopes reconciliation to a topic, limit the search to that topic. Otherwise scan all pages.

Look for:
- Conflicting factual claims
- Outdated entity roles or descriptions
- Older source-backed claims that have been superseded by newer ones

### Step 2: Evaluate each contradiction

For each contradiction:
1. Determine which source is newer
2. Determine which source is more authoritative
3. Distinguish a real contradiction from an evolution in position

### Step 3: Resolve

- If there is a clear winner, rewrite the outdated page and add a `## History` entry
- If it is ambiguous, present both sides to the user
- If it is an evolution, update the page so the progression is visible
