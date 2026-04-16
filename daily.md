# Daily — Create or update today's daily note

Run session startup (L0-L2) for this operation.

## Step 1: Check for existing daily note

Look for `wiki/daily/YYYY-MM-DD.md` for today. If it exists, update it. If it does not, create it using the template in `pages.md`.

## Step 2: Pull calendar events

If Google Calendar tools are available:
- Fetch today's events
- Add a `## Calendar` section with time, title, and attendees
- For meetings with people who have entity pages, link to their entity pages

If not available, skip silently.

## Step 3: Pull tasks and overdue items

Read topic hub pages. Look for `## Open questions` sections that reference deadlines or actions.

If any items are overdue or due today, add them to the `## Focus` section with context.

## Step 4: Check recent activity

Read the last 5 entries in `log.md`. Summarize recent ingest, rewrites, health checks, and synthesized patterns in a `## Activity` section.

## Step 5: Inject conversation context

Scan the current conversation for anything relevant to today:
- What the user is working on
- Decisions made
- People mentioned
- Ideas discussed

Add those to `## Notes` with relevant wiki links.

## Step 6: Surface cross-domain connections

Scan today's notes, recent ingests, and conversation context for non-obvious links to other parts of the wiki:

- Concepts appearing in unexpected topic areas
- Entities connecting across unrelated sources
- Patterns that echo something documented elsewhere
- Ideas from today's conversation that relate to older wiki entries the user may have forgotten

Add findings to a `## Connections` section in the daily note. Each connection should name the two things being linked, explain why the link is interesting, and include wikilinks to both pages.

This is the step where the wiki earns its keep — surfacing links the human wouldn't have noticed. If no meaningful connections emerge, skip the section rather than forcing weak ones.

## Step 7: Report

Tell the user the daily note path and a brief summary of what it contains.
