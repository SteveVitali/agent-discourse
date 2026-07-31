# agent-discourse

Agentic AI tooling to facilitate discourse, packaged as portable
[Agent Skills](https://agentskills.io): rigorous, elite-bar editorial review
of article and essay drafts today; discourse mapping, steelmanning, and
revision tooling on the roadmap.

- **Harness-agnostic** — standard `SKILL.md` directories. Claude Code, Codex,
  Cursor, Gemini CLI, GitHub Copilot, and a growing list of clients load them
  natively; anywhere else (Windsurf, Goose, a human with a terminal), a
  one-line pointer to the skill file works. Where a skill benefits from
  subagents, it binds to whatever isolated-sub-context primitive the harness
  offers and degrades to an explicit sequential fallback discipline where
  there is none.
- **Draft-agnostic** — no assumptions about the piece's genre, venue, or
  format. Calibration targets (venue, audience, draft stage) are inputs or
  inferred and declared, never hardcoded.
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
technically literate Substack readers"). System requirements: file
read/write and **web search + fetch** capability in the harness (the
fact-check and discourse phases are research-heavy — expect a full run to
perform dozens of searches); a subagent primitive is used where available and
is not required.

## Repo layout

```
.claude-plugin/              # plugin + marketplace manifests (Claude Code)
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

- **`revision-check`** — diff-aware follow-up review: did the new draft
  resolve the letter's major issues without introducing regressions?
- **`steelman`** — construct the strongest opposing essay to a draft's
  thesis, as a pre-writing stress test.
- **`discourse-map`** — the discourse-mapping phase as a standalone skill:
  map a question's live discursive terrain before writing begins.
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
