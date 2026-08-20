# Personas — the library, the seed, the profile, the rules

A **persona** is a portable description of a writing identity: how a body of
work sounds, how it is built, what it has already argued, and where its
published corpus can be read. Skills in this repo take a persona as an input so
that generated prose can be made to fit an existing body of work — the
operator's own, a venue's house style, or (under the rules below) someone
else's.

Personas are **cross-project by construction**. Project memory
([`project-memory.md`](project-memory.md)) holds one piece as it moves through
review rounds; a persona outlives every project and is shared by all of them.
That is the whole reason it is not stored inside a project directory.

## The simplest thing that works

A persona is, minimally, **one markdown file**: a description plus pointers to
example writing. Nothing else is required, and every skill must work with only
this:

```
<personas_dir>/
  sjs-substack.md
  house-lrb.md
```

```markdown
# sjs-substack

- **Name / byline:** S. J. Sebastian
- **Mode:** self                      # see "Modes and ethics" below
- **Corpus:** https://sjsebastian.substack.com/archive (all essays; ~7 as of 2026-08)
- **Also:** ~/writing/older-essays/*.md

Philosophy, cultural commentary, the history of ideas. Continuous
argumentative prose in roman-numeral sections; begins from a conceptual error
and replaces it with a more exact model; ends on a stated fact rather than a
judgment. Minimal apparatus — no footnotes natively. First person is rare and
deliberate.

**Never:** listicles, tables, exclamation points, hedge stacks.
```

The only structural requirements are the `# <slug>` heading, a **Corpus**
pointer of some kind (URL, local glob, or "none — description only"), and a
**Mode**. Everything else is free prose: the seed is a human document and the
human is its author.

## The bootstrapped form

A seed is a description; a **profile** is derived, structured knowledge — the
one-time (refreshable) product of `bootstrap-persona`, so that later agents can
write to the persona *without re-researching the corpus*. When a persona is
bootstrapped it becomes a directory:

```
<personas_dir>/<slug>/
  persona.md                 # the seed, unchanged in kind: human-authored, authoritative
  profile/                   # DERIVED — regenerable, never hand-tuned in place*
    index.md                 #   hub: what exists, how to load it, staleness verdict
    voice-print.md           #   measured + described: sentence/paragraph shape, punctuation
                             #   rates, register, signature moves, tics to ration
    structure-repertoire.md  #   how pieces are built: openings, section grammar, endings,
                             #   title/subtitle conventions, apparatus norms, length norms
    lexicon.md               #   signature terms and their meanings; words this author avoids
    exemplars.md             #   short quoted excerpts chosen as calibration targets, each
                             #   with what it demonstrates (see the excerpt rule below)
    positions.md             #   recurring theses and commitments, with the piece each is from
    continuity.md            #   what has already been argued where — for cross-reference,
                             #   and to avoid repeating or contradicting the corpus
    audience-and-venue.md    #   platform mechanics, publication cadence, reader expectations
    calibration.md           #   the held-out fidelity test: prediction, actual, corrections
    provenance.md            #   every piece read, URL, accessed date, coverage, what was skipped
    corpus/
      index.md               #   one line per piece note
      <piece-slug>.md        #   per-piece note (structure map, moves, exemplar quotes)
```

\* Corrections belong in `persona.md` (they are authorial decisions and must
survive a re-bootstrap) or in `profile/calibration.md` (they are findings).
Hand-editing a derived file silently is how a profile starts lying about its
own provenance.

**Migration:** bootstrapping `sjs-substack.md` moves it to
`sjs-substack/persona.md` and writes `profile/` beside it. Both forms are valid
inputs forever; a skill that finds the flat form and no profile simply operates
with less.

## Resolution

`persona` is an input naming a slug (`sjs-substack`), a path, or a URL to a
corpus. Resolve in this order, and **state in the deliverable which was used**:

1. an explicit `personas_dir` input;
2. `personas/` next to the project directory (`<project_dir>/../personas/`);
3. a `personas/` directory at the workspace root (in this repo, the operator's
   local `agent-scratch/personas/`);
4. `~/.agent-discourse/personas/`.

If the slug resolves to a seed with no `profile/`, use the seed and say so — do
not silently launch a corpus research pass inside another skill's run;
`bootstrap-persona` exists for that and the operator chooses when to pay for it.
If nothing resolves, derive an ad-hoc voice specification from whatever the
piece and inputs supply, label it as ad-hoc, and continue. **Never block on a
missing persona, and never invent a persona's positions.**

## What loads when (progressive disclosure)

A profile is bigger than any one task needs. `profile/index.md` is the hub; the
rest loads by role:

| Consumer | Loads | Why |
|---|---|---|
| `draft-from-outline` | `voice-print`, `structure-repertoire`, `lexicon`, `exemplars`, `audience-and-venue`, plus `continuity` when the piece may cross-reference prior work | Writing needs exemplars and measurable targets, not the whole corpus |
| `respond-to-review` | `voice-print`, `lexicon`, `exemplars` | The voice charter is seeded/checked against the persona; taste ties resolve to it |
| `editorial-review` | `structure-repertoire`, `audience-and-venue`, `positions`, `continuity` | Venue bar and the fit/continuity assessment — *not* `voice-print`, which would turn a review into a conformity check |
| `bootstrap-persona` (refresh) | `provenance`, `calibration`, then everything it rewrites | Reuse before recompute |

## Persona vs. voice charter

They are different objects and must not be merged:

- A **persona** is a *prior over a body of work* — durable, cross-project,
  descriptive of many pieces.
- A **voice charter** (`agents/author/voice-charter.md`, per the project-memory
  convention) is a *contract for one piece* — its thesis commitments, its
  protected passages, the register it has actually established.

Precedence: **human guidance > voice charter > persona > model default.** A
piece may deliberately depart from its persona (a philosopher writing a
reported narrative); the departure is recorded in the charter, and the persona
is not thereby amended. A persona changes only when the human changes the seed
or a `bootstrap-persona --refresh` observes a real corpus-level shift.

The editor's use is deliberately narrower than the author's. An editor may hold
a piece to the venue's bar and flag drift from the body of work ("this
contradicts what you argued in *After Bureaucracy* without acknowledging it";
"this is the fourth piece to open on the same move"). An editor may **not**
treat persona divergence as a defect in itself — conformity is not a quality
criterion, and a reviewer that enforces a voice print becomes a style police
rather than a critic.

## Modes and ethics

Every persona declares a **Mode**, and the mode governs what may be done with
it:

| Mode | Meaning | Permitted |
|---|---|---|
| `self` | The operator's own body of work | Everything: draft, publish under the byline |
| `consented` | Another writer's work, used with their permission (note it) | As the permission says |
| `house` | A venue's or tradition's style, not an individual's | Register and structure calibration; the piece is published under its real author's name |
| `study` | A writer's corpus used to understand or emulate craft, without consent for publication | Analysis, exercises, explicitly labeled pastiche |

Rules that hold in every mode:

- **No deceptive attribution.** Text generated against a persona is never
  presented as that person's own writing, and never published under their name,
  absent `self` or explicit permission. `study` output is labeled.
- **Notes, not corpora.** A profile stores *distilled notes and short
  excerpts*, never wholesale copies of the source texts — for the same reason
  project memory never persists raw fetched pages (staleness, bulk, and the
  fact that the durable value is the distillation). Excerpts are the length a
  critic would quote: a sentence or two, up to ~40 words, each with a stated
  analytical purpose and its source URL.
- **Respect access.** Public pages only; honor paywalls, `robots.txt`, and
  platform terms. Material the operator supplies locally is theirs to supply.
- **Living people are not instruments.** A persona of a non-consenting living
  writer may inform register and structure; it may not be used to manufacture
  positions they do not hold, and `positions.md` for a non-`self` persona
  records only what they demonstrably argued, quoted and cited.
- **The human owns the byline.** Persona conditioning is a drafting aid. No
  skill in this repo publishes.

## Provenance and staleness

A living corpus grows, so a profile is a dated observation, not a fact:

- `provenance.md` records, per piece read: title, URL, publication date,
  accessed date, and its role (sampled / held out / skipped, with reason). It
  also records the corpus size at bootstrap time.
- `index.md` carries a **staleness verdict**: bootstrap date, corpus size then,
  and the rule — a profile whose corpus has grown by more than ~25%, or that is
  more than ~6 months old, is stale for voice purposes and should be refreshed
  before a piece leans on it. A stale profile is still usable; the consuming
  skill states its date and the operator decides.
- **Persist** (expensive, stable): per-piece notes, the voice print, the
  measured features, positions with citations, calibration results, provenance.
- **Recompute** (cheap or perishable): anything about the *current* draft;
  audience/reception characterizations older than the round; any claim about
  what the persona "would think" about a new topic — that is inference, not
  memory, and it is labeled as inference wherever it appears.

## The honest limit

Persona conditioning shifts surface features reliably and authorial identity
only partially. Theory-grounded evaluation of inference-time stylistic
personalization finds that few-shot, profile-extraction, and contrastive
methods all score *below* a cross-author baseline on trained authorship
verification even while improving trait-level and judge-level scores, and that
the common metrics barely correlate with one another
([arXiv:2604.26460](https://arxiv.org/abs/2604.26460)); stylometric study of
GPT-4o imitating distinctive literary voices finds in-context examples improve
alignment while imitations still cluster with generic model output
([doi:10.1093/llc/fqaf035](https://doi.org/10.1093/llc/fqaf035)). So a profile
is a set of **checkable constraints and calibration targets**, not a voice
transplant. The `calibration.md` held-out test exists to keep that claim honest
per persona rather than in the abstract, and every consuming skill is expected
to report where it could not meet the profile's targets.
