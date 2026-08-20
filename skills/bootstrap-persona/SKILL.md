---
name: bootstrap-persona
license: MIT
description: "Turn a one-paragraph persona description plus pointers to a body of work into a structured, cited persona profile other agents can write to without re-researching: a corpus census, per-piece close-reading notes, a measured voice print, structure repertoire, lexicon, positions and continuity record, and a held-out calibration test that proves how close the profile actually gets. Use when asked to bootstrap, build, profile, refresh, or 'learn' a writing persona, voice, or house style from an existing body of work."
inputs:
  - name: persona
    required: true
    description: "The persona: a slug of an existing seed file (per conventions/personas.md), a path to one, or a new slug to create. If it does not exist, it is created from the corpus and guidance given here."
  - name: corpus
    required: false
    description: "Pointers to the body of work — archive URLs, individual URLs, local file globs, or a mix. Default: the seed's Corpus field. At least one of the two must resolve to readable text."
  - name: personas_dir
    required: false
    description: "Where the persona library lives. Default: the discovery order in conventions/personas.md."
  - name: mode
    required: false
    description: "'bootstrap' (default when no profile exists): full pass. 'refresh': read provenance, ingest only pieces published or changed since the last accessed date, update the profile with deltas, and re-run calibration on the newest piece."
  - name: usage
    required: false
    description: "Ethical mode — self | consented | house | study (see conventions/personas.md). Default: the seed's Mode field; absent, 'study' is assumed (the most restrictive) and the assumption is reported prominently so the human can correct it."
  - name: sample
    required: false
    description: "How much of the corpus to read closely. Default: everything up to ~12 pieces; beyond that, a recency-weighted sample plus deliberate range coverage, with the coverage fraction stated in the profile."
  - name: calibration
    required: false
    description: "Default true. Run the Phase 4 held-out fidelity test. Disabling leaves the profile unfalsified — its accuracy becomes an assertion, and consuming skills have no measured expectation to check themselves against."
---

# Bootstrap Persona

You are building the durable knowledge that lets a later agent write prose that
belongs to an existing body of work — without reading that body of work again.
The input is a description and some pointers. The output is a **persona profile**
per [`conventions/personas.md`](../../conventions/personas.md): cited,
measured, dated, and tested against a piece it was not allowed to see.

Read the convention first. It defines the layout, the modes, the excerpt rule,
the staleness rule, and the boundary between the human-authored seed and this
skill's derived output. This file is the regimen.

## Operating principles

1. **Read the work; do not infer the writer.** Everything in the profile comes
   from text you actually read this session, cited by URL or path with an
   accessed date. No claim about how this author writes may rest on general
   knowledge of the venue, the genre, or the person's reputation — and if the
   corpus is thin, the profile says so rather than filling in.

2. **Measure what can be measured.** "Long, elegant sentences" is not a
   constraint; "median 24 words, range 6–61, with a short declarative closing
   most paragraphs" is. Count. State the sample the count came from. An
   approximate figure honestly labeled beats a confident adjective.

3. **A profile is falsifiable or it is decoration.** One or two pieces are
   held out before any reading begins and are used to test the profile's
   predictions (Phase 4). What the test reveals is written down — including
   where the profile was wrong.

4. **Style is not only sentences.** The reusable knowledge is as much structural
   and intellectual: how pieces open and close, how sections are grammared, what
   apparatus appears, how long pieces run, which thinkers recur, what has
   already been argued and where. A voice print alone produces pastiche with
   nothing to say.

5. **Distill; never hoard.** Notes and short excerpts, never wholesale copies of
   the corpus — the convention's excerpt rule (a critic's quotation, up to ~40
   words, each with a stated purpose and source). Store the distillation, not the
   text.

6. **Separate description from prescription.** Observations belong in the derived
   profile; instructions about what a future draft *should* do belong in the
   seed, which is the human's document. When you believe a tic should be
   rationed, record the observed frequency and say so as an observation with a
   recommendation attached — do not silently convert taste into law.

7. **The anti-signature matters as much as the signature.** Imitation fails by
   over-production: the move the author makes twice per essay, an imitator makes
   nine times. Every signature move gets a measured rate, and the profile states
   what this author demonstrably never does.

8. **Respect the mode.** `usage` governs everything downstream. Under any mode
   but `self` or `consented`, the profile carries a prominent header stating what
   it may and may not be used for, and `positions.md` records only positions the
   author demonstrably took, quoted and cited.

9. **Report the coverage honestly.** Corpus size, how much was read, what was
   skipped and why, and the staleness verdict. A profile built from three posts
   is useful and must not pretend to be built from thirty.

## Workspace

Everything is written under the persona directory (creating it, and migrating a
flat seed file into it, per the convention):

```
<personas_dir>/<slug>/
  persona.md            # the seed — preserved; only appended to, with a marked note
  profile/
    index.md            # the hub other skills load: what exists, staleness, how to use
    voice-print.md
    structure-repertoire.md
    lexicon.md
    exemplars.md
    positions.md
    continuity.md
    audience-and-venue.md
    calibration.md
    provenance.md
    corpus/
      index.md
      <piece-slug>.md
```

Write each file as its phase completes; do not hold the profile in context and
write it at the end. A corpus pass is long, and the per-piece notes are the
expensive part — losing them to a compaction is the failure this layout prevents.

## Execution model

Six phases. Phase 1 (the corpus read) is the heavyweight one and the natural
place for parallelism.

- **Where the harness supports subagents:** after Phase 0, fan the corpus out —
  one subagent per piece or per small batch — each given the piece's URL or path,
  `references/corpus-note.md`, the excerpt rule, and **the explicit instruction
  never to open the held-out piece** (name it, by URL). Each writes its own
  `profile/corpus/<slug>.md`. Then do Phases 2–5 yourself: synthesis needs one
  mind holding all the notes.
- **Where it doesn't:** read pieces sequentially, writing each note before
  opening the next, and never re-read a piece from memory in a later phase —
  read the note.

Do not synthesize from an archive listing, titles, or search snippets. A voice
print derived from summaries is a plausible fabrication, which is worse than
nothing because everything downstream will trust it.

## Phase 0 — Resolve, consent, census

1. **Resolve the persona.** Locate the seed per the convention's discovery order,
   or create one from `corpus` plus whatever the operator has stated about the
   persona in this request (a seed is a short human document; write it as one,
   and mark it as agent-drafted for the human to correct). Under `mode: refresh`, read
   `profile/provenance.md` and `profile/calibration.md` first: reuse before
   recomputing.
2. **Settle `usage`.** From the seed's Mode, or the input, or default `study`
   with a prominent report. If the mode is `consented`, record who consented and
   how (one line, in `provenance.md`).
3. **Take the census.** Enumerate the corpus: every piece, with title, URL or
   path, publication date, and approximate length. An archive page usually gives
   this; a local corpus gives it from the files. Record the total — the census is
   what makes coverage and staleness checkable later.
4. **Choose the sample.** All of it up to ~12 pieces. Beyond that: weight toward
   recency (voices move), then deliberately cover the range — the longest and
   shortest, each distinct genre or series, and at least one early piece so drift
   is visible. Record inclusions *and* exclusions with reasons.
5. **Reserve the hold-out.** Set aside one piece — two if the corpus has ten or
   more — as the calibration target: recent, representative, not an outlier.
   Record only its title, subtitle, date, URL, and one-line topic. **Do not read
   it, do not fetch it, and do not let a subagent fetch it.** Under `refresh`,
   the newest unread piece is the natural hold-out and doubles as a drift test.
6. Write `profile/provenance.md` (census, sample, exclusions, hold-out, accessed
   dates, mode, consent line) and stub `profile/index.md`.

## Phase 1 — Close reading

For each sampled piece, read it in full and write `profile/corpus/<slug>.md` per
`references/corpus-note.md`: the structural map (how it opens, how sections are
divided and headed, how it ends), its thesis in one sentence, the moves it makes,
apparatus used, length, and two or three short exemplar excerpts with what each
demonstrates. Append a line to `profile/corpus/index.md`.

Notes are per-piece and evidential. Do not generalize here — the temptation to
write "the author always…" after two pieces is what Phase 2 exists to discipline.

## Phase 2 — The voice print

Synthesize `profile/voice-print.md`, `profile/structure-repertoire.md`, and
`profile/lexicon.md` from the notes, following `references/voice-print.md`, which
defines what to measure and how to record it. In outline:

- **Measured features** — sentence length (median and range), paragraph length,
  section count and length, piece length distribution, first-person rate,
  question rate, punctuation rates per 1,000 words (em-dash, colon, semicolon,
  parenthesis), apparatus counts. Each with the sample it was computed over, and
  with the measurement method stated (counted exactly, or estimated from sampled
  passages — both acceptable, conflating them is not).
- **Register and syntax** — described, each description anchored to at least one
  quoted instance: diction level, sentence-shape habits, how emphasis is
  achieved, how abstraction and concretion alternate.
- **Signature moves** — with **measured rates** and one exemplar each; plus the
  **anti-signature list**: what the corpus never does (and the ration guidance
  for each signature, per principle 7).
- **Structure repertoire** — opening patterns (with which pieces used which),
  section grammar, ending patterns, title and subtitle grammar, apparatus norms,
  and typical length by piece type.
- **Lexicon** — recurring terms and the sense the author gives them (a persona's
  vocabulary is often a small conceptual toolkit reused across pieces), plus
  words and constructions conspicuously absent.

Then `profile/exemplars.md`: at most a dozen excerpts, each ≤ ~40 words, chosen
because a drafter can calibrate against them — an opening, a transition, an
emphatic close, a concession, a handling of evidence — each with its source and
what it demonstrates.

## Phase 3 — Positions, continuity, venue

Three files that make a persona more than a style:

- **`positions.md`** — the recurring theses and commitments, each stated in the
  author's terms, quoted, and cited to the piece it comes from. Distinguish
  *held positions* from *recurring preoccupations* (a subject returned to without
  a settled view). Under any mode but `self`, this file is quotation-bound: no
  extrapolated views, no "would probably argue."
- **`continuity.md`** — a chronological record of what has been argued where, so
  a new piece can cross-reference prior work accurately ("as I argued in X"),
  avoid re-arguing settled ground, and notice when it contradicts the corpus.
  One entry per piece: date, title, URL, thesis, what it established, and any
  vocabulary it introduced.
- **`audience-and-venue.md`** — the platform's mechanics and the readership's
  expectations: publication cadence, length norms, sections or series, paywall
  behavior, citation and footnote practice, how comments and replies are handled,
  what the venue's tagline claims. Note where the venue's norms and the author's
  habits diverge, since a drafter must satisfy both.

## Phase 4 — Calibration *(skip if `calibration=false`)*

The falsification step. Follow `references/calibration-protocol.md`:

1. From the hold-out's **title, subtitle, date, and one-line topic only** —
   plus the profile you just wrote — draft ~400–600 words of its opening as the
   persona would. Save it as the prediction, before reading anything.
2. Now read the real piece.
3. Compare: the measured features side by side, then the qualitative
   divergences — the move the profile did not predict, the structure it got
   wrong, the register it overshot, the tic it over-produced.
4. **Correct the profile** where the hold-out shows it wrong, and record every
   correction with its evidence.
5. Where the harness supports subagents, add the blind check: give a fresh
   context the two openings unlabeled and ask which is the author's and why. The
   *reasons* are the signal — record them verbatim as diagnostic; do not treat
   the verdict as a score.

Write `profile/calibration.md`: the prediction, the measured comparison, the
divergences, the corrections made, and a plain statement of how close the profile
got. If the hold-out reveals the profile is badly off, say so in `index.md` — a
profile that knows its own error is usable; one that hides it is not.

## Phase 5 — Package and register

1. **`profile/index.md`** — the hub every consuming skill loads first: persona
   name and mode (with usage restrictions if any), corpus size and coverage
   fraction, bootstrap or refresh date, **staleness verdict** per the convention,
   a one-paragraph characterization of the voice, the file list with one line
   each on when to load it, the calibration verdict, and the known gaps.
2. **Append to `persona.md`** a short, marked note: profile built, date, corpus
   coverage, and anything the human should decide (a mode that was assumed, a
   tic the corpus shows that they may not intend, a genre the profile cannot
   cover). Never rewrite the human's seed prose.
3. **Report to the operator:** corpus size and how much was read, the three most
   distinctive things the profile found (with a quoted instance each), the
   anti-signature list's headline items, the calibration result stated honestly,
   the assumed or confirmed `usage` mode with its restrictions, the staleness
   rule that now applies, and the paths. Name the natural next step —
   `draft-from-outline` with `persona: <slug>` — and state plainly that the
   profile constrains and measures voice rather than reproducing it (the limit is
   in the convention's closing section; do not oversell).
