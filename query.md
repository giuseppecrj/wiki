# Query — Answer questions from the wiki

Run session startup (L0-L1) first. This operation loads L3 as needed.

This is a read-only operation. Do not modify wiki pages during a query unless the user explicitly asks to file the result afterward.

## Step 1: Understand the question

Classify the request:
- What do we know about an entity
- What sources cover a topic
- How a concept works
- The connection between two things
- What changed about an entity
- A summary of a topic
- A broad exploratory question

## Step 2: Search the index

Read `index.md`, then identify the most relevant pages. If the index is not enough, grep `wiki/` for related terms.

Select a focused set of pages to read. Do not read the entire wiki.

## Step 3: Read pages

Read the identified pages. Follow relevant links when the question requires synthesis.

Track which claims come from which page so the answer can be cited precisely.

## Step 4: Synthesize the answer

Present:
- the direct answer first
- citations as aliased wikilinks
- connections across pages
- contradictions when they exist
- gaps when the wiki is incomplete

Keep it concise and synthetic.

## Step 5: Offer to file

If the answer creates a reusable insight that the wiki does not yet capture, offer to save it as a new or updated page.
