# agent-discourse

Agentic AI tooling to facilitate discourse, packaged as portable
[Agent Skills](https://agentskills.io): a drafting regimen that turns an outline
into a real first draft, an elite-bar editorial review board for article and
essay drafts, the author's seat that metabolizes its reviews into the next
draft, a persona system that fits generated prose to an existing body of work,
and the durable project memory that lets them run as a multi-round loop;
discourse mapping and steelmanning on the roadmap.

```
outline ─▶ draft-from-outline ─▶ editorial-review ─▶ respond-to-review ─▶ …
                    ▲                    ▲                   ▲
              persona profile (bootstrap-persona) — shared across every piece
```

- **Harness-agnostic** — standard `SKILL.md` directories. Claude Code, Codex,
  Cursor, Gemini CLI, GitHub Copilot, and a growing list of clients load them
  natively; anywhere else (Windsurf, Goose, a human with a terminal), a
  one-line pointer to the skill file works. Where a skill benefits from
  subagents, it binds to whatever isolated-sub-context primitive the harness
  offers and degrades to an explicit sequential fallback discipline where
  there is none.
- **Draft-agnostic** — no assumptions about the piece's genre, venue, or
  format. Calibration targets (venue, audience, draft stage, persona) are
  inputs or inferred and declared, never hardcoded.
- **Research-grounded** — verification obligations, fresh-context second
  reads, calibration rules, evidence-cited findings: the design decisions
  trace to professional editorial practice and the LLM-reviewer literature
  ([rationale](skills/editorial-review/README.md)).

## The flagship: [`editorial-review`](skills/editorial-review/SKILL.md)

Most agents asked to "review my essay" produce something review-shaped:
agreeable, generic, verbose, and occasionally citing sources that don't
exist. What they don't do reliably is everything an elite editorial board
actually does — verify the claims, find the piece's place in the existing
conversation, test the argument against its strongest objections, and tell
the author the truth at a published-venue bar. `editorial-review` packages
that discipline: hand it a draft, and it runs the full pre-publication
apparatus autonomously, surfacing at the end with a review dossier.

```
draft ─▶ intake: steelmanned piece brief + claim inventory
      ─▶ fact-check (magazine model, claim ledger) ∥ discourse map (originality
         audit, strongest counterargument, reception forecast) ∥ independent
         second read (fresh context, draft only)
      ─▶ argument critique (acceptability · relevance · sufficiency)
      ─▶ craft critique (structure ▸ prose ▸ voice)
      ─▶ editor's letter + anchored rubric + revision roadmap
```

- **Evidence over impressions** — every finding quotes the passage it is
  about; every fact-check verdict carries the URL of a source actually read;
  nothing may be cited that wasn't fetched in-session.
- **Independence over simulation** — no persona review-board theater; instead
  an uncontaminated fresh-context second read (empirically worth ~a third of
  total findings) reconciled against the editor's own, with rejected findings
  shown to the author.
- **Calibration honesty** — verdicts pegged to the target venue's published
  bar; rubric scores entailed by findings via explicit caps; no aggregate
  score to launder a fatal flaw through good prose.
- **Proportional rigor** — the heavyweight phases (fact-check, discourse map,
  second read) are individually opt-out, and line-edit depth scales to draft
  stage, so an early structural draft doesn't pay a copy-edit tax.

The full design rationale, with the practice and literature behind each
phase, is in
[skills/editorial-review/README.md](skills/editorial-review/README.md).

## The other seat: [`respond-to-review`](skills/respond-to-review/SKILL.md)

`respond-to-review` takes the author's chair opposite the board: hand it the
review dossier (plus your own top-level guidance, if any) and it metabolizes
the review into the next draft version plus a point-by-point response letter
— the revise-and-resubmit genre, machine-executed:

```
dossier + guidance ─▶ read draft first (anti-anchoring), load memory
      ─▶ independently verify the review's load-bearing findings
      ─▶ triage every finding into a dispositioned response ledger
         (accept / adapt / rebut / defer / escalate, rationale mandatory)
      ─▶ revise as ordered moves (structure ▸ argument ▸ craft), voice guarded
      ─▶ verify claims the revision introduced
      ─▶ fresh-context gap analysis (coverage · discharge · faithfulness)
      ─▶ drafts/v(N+1).md + response letter
```

- **Findings are input, not commands** — the reviewer is right about the
  symptom and suspect about the cure, so *adapt* (own diagnosis, own
  treatment) is the default posture; rebuttals carry an evidentiary bar;
  identity-level forks escalate to the human, whose guidance outranks the
  review.
- **Moves, not patches** — findings are tracked as a list but discharged
  through global-first revision moves, because per-comment patching is
  novice revision by construction; text no finding touches stays untouched
  (the anti-homogenization rule).
- **Metabolism is proven, not claimed** — every disposition has a rationale,
  every "done" cites its passage in the new draft, and a fresh-context gap
  analysis audits coverage, discharge, and faithfulness (a hedge bolted onto
  an overclaim is not a fix).

Rationale: [skills/respond-to-review/README.md](skills/respond-to-review/README.md).

## The first draft: [`draft-from-outline`](skills/draft-from-outline/SKILL.md)

Ask an agent to "write this up" from an outline and you get the outline back in
sentences: headings promoted to sections, bullets promoted to paragraphs, the
source's citations inherited unchecked, no argument actually built.
`draft-from-outline` is built against exactly that artifact — hand it an outline,
a research dossier, or a pile of notes and it delivers `drafts/v1.md` plus a
short note on what it verified, cut, and decided:

```
outline ─▶ intake: commitments vs. advice vs. material vs. alternatives; claim
           inventory with provenance; gaps; refusals
      ─▶ voice binding (persona exemplars + measurable targets + tic rations)
      ─▶ structure plan: spine of claims, per-section function/entry/exit/budget,
         promise–payoff table, apparatus budget, cut list  ◀ human gate
      ─▶ grounding: verify every claim before it becomes prose
      ─▶ draft section by section against contracts and budgets
      ─▶ consolidation: seams, promises, budget reconciliation, measured voice
      ─▶ fresh-context fidelity audit ─▶ drafts/v1.md + drafting note
```

- **Structure is a decision, not an inheritance** — the outline's order is the
  order research arrived in; the draft's order is chosen for a reader, and every
  deviation is logged with a reason (`fidelity: strict` reproduces the outline
  and reports its problems instead).
- **Nothing enters the draft unverified** — outline citations start at zero
  trust (measured fabrication rates in model-produced references run from 11% to
  57%), claims carry a provenance class and a drafting instruction, and anything
  still unverified ships marked in the text *and* listed in the note.
- **Never invent what only the author can supply** — no fabricated quotations,
  sources, first-person experience, or unlabeled scenes.
- **Prose, provably** — word budgets per section, an apparatus budget, a
  register-hazard list, and a fresh-context audit that applies explicit
  outline-restatement tests and counts voice features against their targets.

Rationale: [skills/draft-from-outline/README.md](skills/draft-from-outline/README.md).

## Fitting a body of work: [`bootstrap-persona`](skills/bootstrap-persona/SKILL.md)

A **persona** ([conventions/personas.md](conventions/personas.md)) is a portable
description of a writing identity — minimally one directory with a `PERSONA.md`:
a description plus pointers to example writing, with an optional `docs/` for
corpus files that aren't on the open web. Every skill takes `persona` as an
input, so generated prose can be made to fit an existing body of work, and a
review can ask whether a piece belongs to one.

```
personas/<persona-id>/
├── PERSONA.md            # required: the human-authored seed (description + corpus pointers)
├── docs/                 # optional: manuscripts and exports the operator supplies
└── profile/              # derived by bootstrap-persona; regenerable
```

`bootstrap-persona` is the one-time (refreshable) research pass that turns that
description into knowledge later agents can use without re-reading the corpus:
a census, per-piece close-reading notes, a **measured** voice print (with rates
and rations for every signature move), a structure repertoire, a lexicon, cited
positions and a continuity record — and a **held-out calibration test** that
predicts an unseen piece's opening, compares it to the real thing, and writes
down where the profile was wrong.

- **Measurement over adjectives** — every line is a measurement with its sample
  or a description with a quoted instance; "elegant and incisive" constrains
  nothing.
- **Falsifiable** — the hold-out is reserved before any reading begins and
  forbidden to every subagent; the calibration file reports the gap rather than
  claiming there isn't one.
- **Voice *and* mind** — positions and continuity are what let a new piece cite
  prior work accurately and avoid contradicting it.
- **Ethics in the artifact** — a required usage mode (`self` / `consented` /
  `house` / `study`) defaulting to the most restrictive, distilled notes rather
  than stored corpora, and no publishing by any skill here.

Rationale: [skills/bootstrap-persona/README.md](skills/bootstrap-persona/README.md).

### The review loop and project memory

The two review skills compose into a multi-round loop — `editorial-review` →
`respond-to-review` → `editorial-review` … , entered either from a hand-written
draft or from `draft-from-outline` — coordinated through durable
**project memory** ([conventions/project-memory.md](conventions/project-memory.md)):
versioned drafts and a `correspondence/` record (dossiers, response letters)
shared by both seats, plus strictly private per-agent memory
(`agents/editor/`, `agents/author/`) holding each side's distilled research
notes, outlines, drafting dossiers, and working ledgers. Expensive deep research
persists and compounds across rounds; deliberations never cross the boundary —
the editor of v2 must not inherit the author's rationalizations, nor vice versa.
Personas sit deliberately *outside* project memory, since a voice outlives any
one piece, and each role loads only the part of a profile its job justifies (the
editor never loads the voice print — a reviewer enforcing a style is running a
conformity check). What each side may reuse vs. must recompute (and why) is
specified in the conventions.

## Install

**As a Claude Code plugin:**

```
/plugin marketplace add SteveVitali/agent-discourse
/plugin install agent-discourse@agent-discourse
```

**Or by symlink**, for any client that discovers skills on disk
(`~/.claude/skills/`, a project's `.agents/skills/`, etc.):

```bash
git clone https://github.com/SteveVitali/agent-discourse.git ~/agent-discourse
ln -s ~/agent-discourse/skills/* ~/.claude/skills/
```

**Clients without native skill support** (e.g. Windsurf): a one-line workflow
or rule pointing at the skill file is enough — *"Read and follow
`<path>/skills/editorial-review/SKILL.md`"*.

Each skill declares its inputs in `SKILL.md` frontmatter; state them in
natural language ("review drafts/essay.md — near-final draft, aimed at
technically literate Substack readers"; "draft from notes/surveillance-outline.md
as persona sjs-substack, ~6,000 words"). System requirements: file
read/write and **web search + fetch** capability in the harness (the
fact-check and discourse phases are research-heavy — expect a full run to
perform dozens of searches); a subagent primitive is used where available and
is not required.

## Repo layout

```
.claude-plugin/              # plugin + marketplace manifests (Claude Code)
conventions/
├── project-memory.md     # one piece across rounds: layout, boundary rules, persistence
└── personas.md           # the persona library: seed, profile, resolution, modes, ethics
skills/<skill-name>/
├── SKILL.md             # entry point (Agent Skills format: frontmatter + regimen)
├── README.md            # design rationale (research basis for every mechanism)
└── references/          # protocols/templates loaded on demand from SKILL.md
```

One predictable entry filename means an agent (or tool) pointed at `skills/`
knows where every skill starts; everything else in a skill directory is
progressive-disclosure material referenced from its `SKILL.md`.

## Design principles

- **Durable dossier over context** — every phase writes its complete report
  to disk (piece brief, claim ledger, discourse report, second read, editor's
  letter); files are the only inter-phase interface, so the regimen survives
  compaction and runs identically with or without subagents.
- **Verification over assertion** — claims are checked in the regime where
  models are reliable (citation against a fetched source), with a closed
  verdict vocabulary; an unverified claim is never reported as verified.
- **Judgment independence** — the second read happens in a fresh context
  given only the draft where the harness supports it, and always argues from
  what is on disk rather than memory of forming earlier findings.
- **Calibration honesty** — the elite bar is stated and held: no leniency
  drift, no aggregate scores, no findings without quoted anchors, and
  "report against interest" when research vindicates the piece.
- **Progressive disclosure** — the hub `SKILL.md` stays lean; protocols,
  rubric, and templates load only when their phase runs.
- **Proportional rigor** — every heavyweight phase is opt-out, so a quick
  structural read doesn't pay the full ten-phase tax.
- **Measured, not asserted** — where a property can be counted (claim verdicts,
  word budgets, punctuation rates, signature-move frequencies), the skills count
  it and report the number next to its target; where it can't, they say so.

## Authoring a new skill

1. Create `skills/<name>/SKILL.md` with frontmatter: `name`, `license`,
   `description` (what it does *and* when to use it), and `inputs` (each with
   `name`, `required`, `description`). Keep the body harness-neutral — no
   tool-specific directives in skill files; subagent use is phrased as
   "where the harness supports subagents …, where it doesn't …" with an
   explicit fallback discipline.
2. Write instructions that are **concrete enough to verify** ("every ledger
   entry carries a URL read this session"), and calibrated to a frontier
   model: specify *what* and *why*, not keystroke-level *how*.
3. If the skill nears the spec's ~500-line ceiling for `SKILL.md`, split
   protocols and templates into `references/` files loaded on demand.
4. Pair every skill with a `README.md` design rationale: the failure modes it
   is built against, the literature or professional practice grounding each
   mechanism, degrees-of-freedom calibration, and honest limitations.
5. No assumptions about the draft, venue, or harness anywhere: discover from
   the piece or take it as an input.

## Roadmap

- **`steelman`** — construct the strongest opposing essay to a draft's
  thesis, as a pre-writing stress test.
- **`discourse-map`** — the discourse-mapping phase as a standalone skill:
  map a question's live discursive terrain before writing begins.
- **Loop orchestration** — drive `editorial-review` ↔ `respond-to-review`
  rounds to a convergence criterion, in the style of the sibling repo's
  `orchestrate-build`.
- Structured-dialogue tooling for exchanges between positions.

## Related

- **[Agent Skills](https://agentskills.io/specification)** — the open format
  these skills conform to.
- **[SteveVitali/agent-skills](https://github.com/SteveVitali/agent-skills)**
  — the sibling collection: rigorous engineering process for AI coding
  agents (implement-spec, self-review, review-pr, …). Same conventions, same
  design philosophy; this repo applies them to discourse rather than code.
- **[anthropics/skills](https://github.com/anthropics/skills)** — Anthropic's
  reference collection; mostly *capability* skills (documents, design,
  testing tools).

## License

[MIT](LICENSE)
