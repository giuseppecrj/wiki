# Challenge — Red-team an idea against vault history

Run session startup (L0-L1) first. This operation loads L3 selectively.

The entire point is to pressure-test. Do not be agreeable. Cite specific wiki pages. If you find nothing contradictory, say so honestly, but search thoroughly first.

## Step 1: Identify the claim

Parse the user's idea, plan, or assumption from the request or recent conversation context.

Restate the claim clearly. Extract the key premises, the assumptions that must be true for the claim to hold.

## Step 2: Search for counter-evidence

For independent workstreams, investigate in parallel where practical:

- Search entity and concept pages for past decisions that contradicted similar thinking
- Search source pages for case studies where a similar approach failed
- Check `## History` sections for positions that were later reversed
- Search for sources or concept pages where the user or a cited authority held the opposite position
- Look for `## User's take` sections that conflict with the current claim
- Check whether the approach was tried before and abandoned
- Look for trade-offs and constraints documented in the wiki that the current plan ignores

## Step 3: Present the red-team

Structure the output as:

```markdown
## Challenge: [restated claim]

### Your position
[Clear restatement of what the user is proposing and the premises behind it]

### Counter-evidence from the wiki

**[Counter-point 1]**
[[page-slug|Page Title]] says: "[relevant quote or claim]"
This contradicts your premise that [specific premise].

**[Counter-point 2]**
[[page-slug|Page Title]] documents that [relevant fact].
Your plan doesn't account for [specific constraint].

### Blind spots
- [Gap 1]
- [Gap 2]

### Verdict
- **Supported**: The wiki has no meaningful counter-evidence. The plan is consistent with everything documented.
- **Contested**: The wiki has specific counter-evidence. Address these before proceeding.
- **Untested**: The wiki doesn't have enough information to challenge this effectively.
```

## Step 4: Log

If today's daily note exists, add a brief entry: `Challenged: [claim]. Verdict: [supported/contested/untested].`
