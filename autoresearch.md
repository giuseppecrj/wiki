# Autoresearch — deep research on a question via parallel exploration

Run session startup (L0-L1) first. This operation loads L3 heavily and writes to disk during the run.

Autoresearch is the wiki's parallel-exploration operation. Given a question the user wants researched in depth, it forks several distinct hypotheses, explores each in isolation with its own context budget, prunes weak branches, and synthesizes the strongest findings into a final answer. Every run leaves a preserved research folder the wiki can reference later.

This operation is distinct from the other read operations:
- `query` answers a question from what the wiki already knows.
- `challenge` red-teams a specific claim by hunting for counter-evidence.
- `synthesize` looks backward across recent ingests for emergent patterns.
- **`autoresearch` looks forward**: picks a question and builds new understanding by forking multiple research angles and letting them run in parallel.

The architectural inspiration is [[evo|evo-hq/evo]]'s autoresearch loop, which in turn descends from [[andrej-karpathy|Andrej Karpathy]]'s autoresearch pattern. The wiki version preserves the orchestrator-plus-isolated-workers shape the [[anti-swarm-consensus|anti-swarm consensus]] endorses: one orchestrator, N workers, shared state via files, no peer coordination.

## Invocation

```
/wiki autoresearch <question> [angles=N] [budget=N] [stall=N]
```

- `angles` — number of parallel research angles (default 3, max 5)
- `budget` — wiki-page reads + external fetches per angle (default 5)
- `stall` — consecutive stall rounds before auto-stopping (default 2)

## Step 1: Discover

Parse the question. The orchestrator does this alone, before any subagent spawns.

Extract:
- **The actual claim or decision** the user is trying to resolve
- **The key premises** that must hold for the claim to be true
- **The domains** the question touches — which topic areas, entity clusters, concept clusters
- **What counts as evidence** — citations, contradictions, precedent, counter-examples, absence of counter-evidence

Write the plan to `wiki/research/YYYY-MM-DD-<slug>/plan.md`. The slug is a kebab-case derivation of the question, truncated to ~40 characters. If the folder exists, suffix with `-N` until it doesn't.

The plan file uses this frontmatter:

```yaml
---
type: research
title: "Research question (verbatim)"
date_started: YYYY-MM-DD
status: active | complete | abandoned
angles: N
---
```

The body lists the angles, each as a short hypothesis the subagent is assigned to test.

## Step 2: Instrument baseline

The orchestrator runs one direct-answer pass using `query.md`'s protocol. This is the baseline — the best answer the wiki can give right now, without parallel exploration.

Record the baseline in `plan.md` under `## Baseline`:
- direct answer
- citation count
- confidence (low | med | high)
- gaps noted

The baseline is what the parallel angles must beat. If autoresearch doesn't produce stronger evidence than the baseline, the run was wasted.

## Step 3: Generate angles

The orchestrator writes N angle briefs, one per subagent. Each brief contains:

- **Hypothesis** — a specific claim the angle is testing
- **Starting pages** — 2-3 wiki pages the angle should start from
- **Adjacent clusters** — topics/entities/concepts to explore outward
- **External sources allowed** — which URLs, repos, or search terms are in scope
- **Success signal** — what kind of evidence would strengthen this angle
- **Abandon signal** — what would tell the subagent the angle is dead

Good angles are **distinct hypotheses, not repetitions of the same premise with different words**. Bad angles:
- "Research OpenWork more deeply" (restates the question, no hypothesis)
- "Find information about skill distribution" (domain, not hypothesis)

Good angles:
- "OpenWork's unsigned bundle model is the fatal gap and pkts's signing will be the decisive differentiator"
- "The skill-distribution market consolidates around one winner by 2027 and that winner will be whichever tool best matches the OCI substrate already in production"
- "Cross-harness compatibility is the most important axis and pkts's current Claude-Code-first adapter priority is wrong"

Each angle gets its own folder: `wiki/research/YYYY-MM-DD-<slug>/angle-1/`, `angle-2/`, etc. The brief is written to `brief.md` in each folder.

## Step 4: Run parallel exploration

Spawn N subagents, one per angle. Each subagent:

1. **Reads** the brief, the baseline from `plan.md`, and any `notes.md` files already committed by other angles (sibling angle folders in the same research folder).
2. **Explores** within its budget — reads starting pages, follows links, fetches external sources if in scope. Uses the [[ingest|ingest]] flow's "read source" step for anything external.
3. **Writes** a running `notes.md` in its folder as evidence accumulates. This file is append-only during the round — the subagent adds findings; it does not rewrite earlier notes. Other subagents may read it.
4. **Tracks** citations explicitly — every claim the subagent makes has a wikilink back to its source (a wiki page or an external URL).
5. **Returns** a structured finding at the end of its budget: hypothesis confirmed / contested / untested, evidence list, confidence rating, any contradictions surfaced.

**Explicit rule: no peer coordination.** Subagents read each other's `notes.md` but do not message each other. The orchestrator is the only coordinator. This matches the [[anti-swarm-consensus|anti-swarm architecture]] evo ships — hierarchical decomposition with shared-state coordination, not peer swarms.

## Step 5: Gate and prune

After each round (all N angles finished one iteration), the orchestrator scores angles on three axes:

- **Citation density** — unique citations per budget unit spent
- **Contradiction surface** — did the angle surface contradictions the baseline missed?
- **Cross-cluster reach** — did the angle connect clusters the baseline kept separate?

Angles that score low on all three get **pruned** — marked `status: abandoned` in the angle folder's `brief.md`, no more budget spent. Angles that score high get another round if budget remains and the `stall` counter hasn't hit.

The `stall` counter tracks consecutive rounds with no new citations across any surviving angle. Once `stall` hits the threshold, the run auto-stops.

## Step 6: Synthesize

The orchestrator reads every angle's `notes.md` and produces the final answer in `wiki/research/YYYY-MM-DD-<slug>/synthesis.md`.

Structure:

```markdown
---
type: research-synthesis
title: "Research question"
date_completed: YYYY-MM-DD
---

## Question
Restated clearly.

## Verdict
One of: **Supported** | **Contested** | **Untested** | **Updated** (with a one-line summary).

## Evidence summary
Strongest citations, grouped by the claim they support.

## Counter-evidence
Anything that weakened or contested the claim, with citations.

## What changed vs. the baseline
Explicit diff between what the wiki knew before the run and what it knows now.

## Confidence
Low | medium | high, with one sentence on why.

## Open threads
Angles that ran out of budget but still had signal. These are candidates for a follow-up run.
```

## Step 7: Codification check

Before returning the synthesis to the user, apply the [[synthesize|synthesize]] codification gate to each finding:

- **Has this pattern appeared 3+ times across the wiki?** If yes, it deserves a concept page.
- **Is this a decision the user has just made?** If yes, propose a `## Decisions` entry on the relevant entity/concept page (see `ingest.md` save-decisions flow).
- **Did the run surface contradictions?** If yes, propose a `/wiki reconcile` follow-up.
- **Did the run reveal a pattern that should become a protocol or skill?** If yes, propose codification as a concrete next artifact — not a future intent.

Each proposal is a concrete artifact the user can approve or reject. Don't just list the patterns; name what should happen to each one.

## Step 8: Report

Present the synthesis to the user. Include:

- **The verdict** — the one-word answer to their question
- **The strongest evidence** — 3-5 bullets, each a cited claim
- **What's new** — the diff from baseline, the research actually produced
- **The codification proposals** — what to file, where, why
- **The research folder path** — so they can re-read the angle notes later

Also append one entry to `log.md`:

```
## [YYYY-MM-DD] autoresearch | <question>

- Research folder: wiki/research/YYYY-MM-DD-<slug>/
- Angles: N run, M pruned, K produced strong evidence
- Verdict: <verdict>
- Proposals: <list of codification proposals>
```

## Preserved research folders

Unlike evo (which discards dead worktrees), **wiki research folders are preserved**. Failed angles become future evidence. Matches the wiki's append-only philosophy.

The folder structure after a complete run:

```
wiki/research/
└── 2026-04-16-will-openwork-beat-pkts/
    ├── plan.md                 # the original question + baseline
    ├── synthesis.md            # the final answer
    ├── angle-1/
    │   ├── brief.md            # the hypothesis
    │   └── notes.md            # append-only evidence log
    ├── angle-2/
    │   ├── brief.md
    │   └── notes.md
    └── angle-3/
        ├── brief.md            # status: abandoned (pruned round 2)
        └── notes.md
```

Research folders can be queried in future `/wiki query` calls and surfaced in `/wiki synthesize` passes. Over time `wiki/research/` becomes its own corpus — the record of what has been deeply investigated.

## What autoresearch is NOT

- **Not a replacement for `query`.** `query` is fast and read-only; autoresearch is slow and writes to disk. Use `query` for questions the wiki can already answer. Use autoresearch when the question needs new synthesis or the current wiki state is thin on the topic.
- **Not a replacement for `challenge`.** `challenge` pressure-tests one specific claim; autoresearch explores multiple hypotheses in parallel. They compose — one autoresearch angle can invoke a `challenge`-style pass against its hypothesis.
- **Not a swarm.** No peer coordination. One orchestrator, N isolated workers, shared state via files. This is the architecture the [[anti-swarm-consensus|consensus]] says works; it is not a multi-agent coordination protocol.
- **Not a background job.** Autoresearch runs in the current conversation. It's not a cron. The user sees each round's progress and can interrupt.
