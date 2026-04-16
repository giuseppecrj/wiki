# Synthesize — Surface unnamed patterns

Run session startup (L0-L1) first. This operation loads L3 heavily.

## Step 1: Determine scope

Default to sources ingested in the last 30 days unless the user specifies another timeframe.

Read `log.md` to identify:
- source pages ingested in that period
- entity, concept, and topic pages created or rewritten in that period

## Step 2: Read the sources

Read the source pages and any entity, concept, or topic pages materially touched during the period.

## Step 3: Scan for patterns

Look for:
- concepts appearing across unrelated sources
- entities converging across multiple contexts without explicit connection
- conclusions the wiki implies but never states outright
- directional trends in the user's recent intake

## Step 4: Present the pattern report

For each pattern, provide:
- evidence
- interpretation
- suggested action

## Step 5: Codification check

For each surfaced pattern, ask:

- **Has this pattern appeared 3 or more times?** If so, it has crossed the threshold — it should become a concept page, not just a mention in a report.
- **Is this pattern something the user acts on repeatedly?** If so, it may deserve to become a protocol (a documented process in `CLAUDE.md`) or a companion skill.
- **Has this same pattern been surfaced in a previous synthesize pass?** If the wiki keeps rediscovering the same pattern, the pattern needs to be codified now — not logged again.

Propose codification as a concrete next action, not a future intent. Name the artifact: "Create concept page `wiki/concepts/<slug>.md`" or "Add protocol to `CLAUDE.md`" or "This pattern is a candidate for a companion skill."

## Step 6: Offer to file

If a pattern deserves permanence, offer to create a concept page, add backlinks from relevant sources, and update topic hub overviews.
