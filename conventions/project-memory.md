# Project Memory — layout and boundary rules

The durable, on-disk memory for one piece of writing as it moves through
review rounds. Skills in this repo read and write it; it is what lets a later
agent reuse expensive research instead of recomputing it, while keeping the
author's and editor's judgment strictly separated.

## Layout

One directory per piece ("project"). Location is the operator's choice
(`project_dir` input); by default skills create it next to the original draft,
named after the piece's slug. The layout is location-independent — the
manifest makes it identifiable.

```
<project_dir>/
  manifest.md                  # title, slug, current version, round log
  drafts/
    v1.md                      # immutable once a review or response exists for it
    v2.md
  correspondence/              # the SHARED record — everything formally delivered
    v1-review/                 #   editorial-review dossier for drafts/v1.md
      editors-letter.md        #   (+ claim ledger, discourse report, second read)
    v1-response.md             #   respond-to-review's response letter for that review
    v2-review/
  agents/                      # PRIVATE per-agent memory — never cross-read
    editor/
      research/
        index.md               #   one line per source note
        sources/<slug>.md      #   distilled source notes (see format below)
      notes.md                 #   editor's own working notes across rounds
    author/
      research/                #   same structure as editor's
      ledgers/
        v1-response-ledger.md  #   frozen response ledger per round
      voice-charter.md         #   living doc: voice, commitments, protected strengths
```

## The boundary rule

Context separation is what keeps rounds honest: the editor of v2 must not
inherit the author's rationalizations, and the author must not inherit the
editor's undelivered deliberations.

- Every agent may read **`drafts/`**, **`correspondence/`**, and
  **`manifest.md`** — the shared record. This mirrors real practice: reviewers
  see the author's response memo; authors see the delivered review, and both
  see every draft.
- An agent may read and write **only its own** `agents/<name>/` directory.
  Reading another agent's directory is a boundary violation even when it would
  save work — the savings is the contamination.
- Delivered artifacts are **immutable**: once a review dossier or response
  letter is in `correspondence/`, later rounds append new artifacts; they
  never edit old ones. Same for `drafts/vN.md` once reviewed or responded to.

## Source notes: the persisted fruit of deep research

The expensive part of web research is discovery and distillation, not
re-fetching a known URL. What gets persisted is the distilled note:

```markdown
# <source-slug>
- **URL:** <url> · **Accessed:** <date> · **For:** <round/purpose>
- **What it establishes:** <the claims this source supports or defeats, precisely scoped>
- **Key passages:** <short quotes with enough context to reuse without re-reading>
- **Reliability:** <primary/secondary; independence; known bias>
- **Relevance to this project:** <which findings/claims/sections it bears on>
```

Append one line per note to `research/index.md` (`- [slug](sources/slug.md) —
what it settles`). Before any new research pass, read the index first and
reuse; add notes for every source genuinely read; update a note rather than
duplicating it.

## Persist vs. recompute

**Persist** (expensive to reproduce, stable across rounds):
- Distilled source notes and the research index.
- Decisions with their rationales (frozen response ledgers, delivered
  dossiers and letters) — the record of *why*, which no re-read of the draft
  can reconstruct.
- The author's voice charter and project intent.
- The manifest's round log (what happened when, at which version).

**Recompute always** (cheap, or poisoned by staleness):
- Any judgment about the *current* draft's text — always re-read from disk;
  never trust a memory artifact's characterization of prose that may have
  changed.
- Verification verdicts for any claim whose wording, scope, or context
  changed since the note was written.
- Reception forecasts and discourse "current state" summaries older than the
  round they were written in — discourse moves; source notes carry accessed
  dates so staleness is checkable.

**Never persist**: transcripts of raw fetched pages (re-fetch by URL when
needed); judgments about draft text embedded outside the round-frozen
artifacts; anything whose only purpose is to spare a future agent from
reading the draft itself.

## Versioning

Drafts are `v<N>.md`, monotonically increasing; the current version is the
highest `N`, recorded in the manifest. A review of `vN` lives at
`correspondence/vN-review/`; the response that produces `v(N+1)` lives at
`correspondence/vN-response.md`. Round log entries in the manifest are
one-liners: date, event (`review v2 delivered`, `response v2 → v3`), actor.
