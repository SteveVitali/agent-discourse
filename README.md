# agent-discourse

Agentic AI tooling to facilitate discourse. The first tool is
**`editorial-review`**: an agent skill that takes a pointer to an article
draft and returns the review an elite publication's editorial board would
produce before publication — fact-checking, discourse mapping, argument and
craft critique, an independent second read, an anchored rubric, and a
revision roadmap.

## Repository layout

```
skills/
  editorial-review/
    SKILL.md                           # the regimen: principles + 7 phases
    references/
      verification-protocol.md         # magazine-model fact-checking + claim ledger
      rubric.md                        # 8 anchored dimensions + calibration rules
      editors-letter-template.md       # the anchor deliverable's structure
.claude/skills/editorial-review        # symlink → skills/editorial-review
```

The symlink makes the skill live for Claude Code sessions inside this repo.
To use it elsewhere, symlink (or copy) the skill directory into a project's
`.claude/skills/` or globally into `~/.claude/skills/`:

```sh
ln -s "$(pwd)/skills/editorial-review" ~/.claude/skills/editorial-review
```

## Usage

Invoke it directly or just ask for a review — the skill triggers on requests
to review, critique, or edit a draft:

```
/editorial-review ./drafts/my-essay.md
Review ./drafts/my-essay.md — target audience is technically and
philosophically literate Substack readers; this is a near-final draft.
```

Providing the target venue/audience and draft stage sharpens calibration; the
skill infers them (and says so) if omitted. Output is a review dossier written
next to the draft: an editor's letter plus a claim ledger, discourse report,
and second-read file. Expect a full run to be slow — it deliberately performs
dozens of web searches.

The skill assumes a frontier-level model (Opus/Fable class or above) and a
harness with web search/fetch and, ideally, subagents. It degrades gracefully
to a sequential single-agent regimen when subagents are unavailable.

## What "editorial review" means here — and why

The skill was designed from research into how elite review actually works,
then hardened against the documented failure modes of LLM reviewers. Its
regimen merges four professional traditions:

1. **The magazine fact-checking model** (New Yorker-style): every claim
   linked to a source, quotes called back, the factual context behind
   evaluative claims researched, triage under deadline
   ([KSJ Handbook](https://ksjhandbook.org/fact-checking-science-journalism-how-to-make-sure-your-stories-are-true/the-three-models-of-fact-checking/),
   [CUNY fact-checking guide](https://researchguides.journalism.cuny.edu/factchecking-verification/fact-check-your-work)).
   Realized as Phase 1's tiered verification protocol and claim ledger with
   six defined verdicts.

2. **Academic peer review**: originality, significance, validity, soundness
   of method, contextualization in prior work; findings split major/minor,
   ranked, and actionable
   ([Taylor & Francis reviewer checklist](https://editorresources.taylorandfrancis.com/reviewer-guidelines/review-checklist/),
   [NeurIPS reviewer guidelines](https://neurips.cc/Conferences/2025/ReviewerGuidelines),
   [constructive-review guides](https://pmc.ncbi.nlm.nih.gov/articles/PMC12702561/)).
   Realized as Phase 2's originality audit and Phase 3's substance critique.

3. **The levels-of-edit taxonomy** from professional editing — developmental
   → line → copy, in that order, because polishing prose that won't survive
   structural surgery is wasted work
   ([Jericho Writers](https://jerichowriters.com/types-of-editing-how-to-choose/)).
   Realized as Phase 4's three-altitude craft critique, with line-level depth
   scaled to draft stage.

4. **Argumentation theory**: informal logic's acceptability / relevance /
   sufficiency standard and scheme-specific critical questions, rather than
   fallacy name-dropping
   ([Walton-style schemes and critical questions](https://link.springer.com/article/10.1007/s10503-020-09512-4)).
   Realized in Phase 3's argument-skeleton reconstruction and load-bearing
   inference tests.

### Hardening against known LLM-reviewer failure modes

The literature on LLM-generated review documents specific, recurring
failures; each has a countermeasure wired into the skill as a hard rule:

| Documented failure | Countermeasure in the skill |
|---|---|
| Sycophancy / leniency drift | Calibration pegged to the elite venue bar; "a review where nothing important is wrong is a failed review"; no aggregate score to hide behind |
| Generic, any-essay feedback | Every finding must quote a specific passage; portable comments are explicitly banned |
| Verbosity — many shallow points (LLMs emit ~4× the comments of human reviewers) | 3–7 ranked major issues, each developed in depth; everything else demoted to minor/line notes |
| Hallucinated sources | Nothing may be cited that wasn't fetched and read in-session; failed verification yields "unverifiable," never a guessed citation |
| Superficial summary-as-review | The steelmanned "piece's project" section proves comprehension; the second read cross-checks that the thesis was even transmitted |
| Prosecutorial bias | "Report against interest": findings that vindicate the piece get equal prominence |

### Why not a simulated review board?

A tempting design is a panel of persona reviewers (the "harsh methodologist,"
the "prose stylist") debating under a lead editor. The evidence doesn't
support it: persona simulations like
[AgentReview](https://agentreview.github.io/) are built to *study* reviewer
bias (finding, e.g., ~37% decision variation attributable to it), not to add
rigor, and role-play personas mostly re-skin one model's judgment. What
measurably helps is **independence and fresh context**:
[Liang et al.](https://arxiv.org/abs/2310.01783) found two human reviewers of
the same paper overlap on only ~a third of their points — coverage comes from
uncontaminated second opinions, not from staged debate.

So the architecture is a single orchestrating editor with three
fresh-context delegations, each earning its isolation:

- **Verification** and **discourse mapping** (Phases 1–2) are web-research
  heavy; running them in subagents keeps hundreds of fetched pages out of the
  lead's context, which is preserved for judgment.
- The **second read** (Phase 5) sees *only the draft* — no brief, no prior
  findings — making it a genuinely independent reviewer whose overlap with
  the lead marks high-confidence findings and whose divergence marks blind
  spots (including the diagnostic case where it reads a different thesis).
- File-based phase reports (`<draft>-review/` dossier) are the coordination
  medium, so the same regimen runs sequentially when no subagent tooling
  exists.

The rubric (8 dimensions, 1–5) exists to summarize, not to judge: anchors are
behavioral, 5s require a named published exemplar, scores must be entailed by
findings (a contradicted load-bearing claim caps *Evidence & accuracy* at 2),
and there is deliberately no aggregate number — averaging would launder a 1
in argument rigor through a 5 in prose.

## Roadmap

Candidate future tooling for this repo:

- **`revision-check`** — diff-aware follow-up review: did the new draft
  resolve the letter's major issues without introducing regressions?
- **`steelman`** — construct the strongest opposing essay to a draft's
  thesis, as a pre-writing stress test.
- **`discourse-map`** — the Phase 2 engine as a standalone skill: map a
  question's live discursive terrain before writing begins.
- Interlocutor/dialogue tooling for structured exchanges between positions.
