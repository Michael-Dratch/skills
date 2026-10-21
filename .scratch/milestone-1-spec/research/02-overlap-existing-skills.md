# Overlap with existing skills and output styles

Research for [Overlap with existing skills and output styles](../issues/02-overlap-existing-skills.md).

## Sources

- Matt Pocock skills v1.3.1: `~/.claude/plugins/cache/claude-plugins-official/mattpocock-skills/1.3.1/skills/` (abbreviated `MP/` below). The `~/.claude/skills/` symlinks into `~/.agents/skills/` are byte-identical to these (checked with `diff -rq`), except five non-MP skills: `rubber-duck`, `eng-log`, `dry-prose`, `oblique-strategies`, and `find-skills`.
- Anthropic `learn` skill: `~/.claude/skills/synced/<id>/learn/SKILL.md`.
- Output styles: https://code.claude.com/docs/en/output-styles (fetched 2026-10-20).

## Summary

- Nothing installed does **commit-first plus code-verified plus spaced** work. Each existing thing has at most one of the three.
- Closest to Principle 1 (withhold, then reveal): `rubber-duck` (no solutions until the user releases it). The Learning output style withholds only the code for one spot, and its `Guidance` text is a hint.
- `grilling` is the opposite of Principle 1: every question comes with Claude's recommended answer.
- `learn` contradicts Principle 2: it tells Claude to "hold the line" when the learner says "just tell me".
- The cheapest wins are formats: the ADR and learning-record templates, `GLOSSARY.md`, `0001-slug.md` numbering, lazy creation, and `disable-model-invocation: true` for developer-invoked skills.
- Name collision: `MP/engineering/triage` is issue-tracker triage. Do not ship a slash command called `/triage`.

## Overlap: design-first

| Existing thing | What it does | What it lacks vs. this collection | Reuse recommendation |
|---|---|---|---|
| Learning output style (docs) | Claude builds routine code. At "a real design decision" it leaves a `TODO(human)` marker and a "Learn by Doing" request (Context / Your Task / Guidance), waits, then gives one `Insight` on the human's code. | Claude has already shaped everything around the gap, so the developer is anchored. The `Guidance` field says "what to weigh", which is a hint (breaks Principle 1). The gap is chosen by Claude's judgment of "real design decision", with no reversibility, blast-radius, or novelty triage. Nothing persists. No skip protocol. Works at code level, not design level. | Complement it, don't depend on it. Borrow the 3-part request shape (context / your task / what to decide) for the developer's sketch prompt, but leave out the weighing guidance. |
| Explanatory output style (docs) | Adds 2–3 point `★ Insight` blocks before or after the code Claude writes. | Pure telling, after the fact. If it is active in the main session, it can reveal Claude's reasoning before the developer commits. | The SKILL.md must say: no Insight blocks or reasoning until the reveal step. The docs say styles apply to the main conversation and forks but **not** to other subagents, so running Claude's independent design in a non-fork subagent also keeps style output out of it. |
| `MP/engineering/codebase-design/DESIGN-IT-TWICE.md` | Spawns 3+ subagents, each designing a radically different interface under its own constraint. Presents the designs one after another, compares them on depth, locality, and seam, then gives an opinionated recommendation. | The human never designs. It is Claude-vs-Claude, ending with Claude's verdict. No commit-first, no persistence. | Reuse the mechanics: an independent subagent design (solves the "don't read the sketch first" problem in brief §11) and the per-design output fields (interface, usage, what's hidden, trade-offs). Replace "give your recommendation" with "diff against the developer's sketch". |
| `MP/productivity/grilling` | Design-tree interview in rounds. Each question comes with "➡️ your recommended answer", worded so "yes" accepts it. Facts are the agent's job, decisions are the user's. | Anchors by design, the opposite of Principle 1. Batched rounds (this map's Notes prefer one question at a time). No verification, no persistence. | Reuse the facts-vs-decisions split ("never ask the user what you can look up"). Do **not** reuse recommended answers or batched rounds. |

## Overlap: checkpoint (change-impact drill)

| Existing thing | What it does | What it lacks vs. this collection | Reuse recommendation |
|---|---|---|---|
| `MP/engineering/domain-modeling` ("Cross-reference with code") | When the user states how something works, check the code and surface contradictions. | Done ad hoc during design talk. No prediction-first probe, no accuracy record, no per-Area tracking. | Same verify-against-reality move as Principle 4. Reuse the phrasing pattern: "Your code does X, but you just said Y. Which is right?" |
| `MP/productivity/teach` | Stateful learning workspace. Names fluency vs. storage strength. Uses retrieval, spacing, and interleaving as lesson-design goals. Has quiz rules (equal-length answers, rotate the correct position) and a tight feedback loop. | Topic learning, not the Theory of a system. Answers are checked against curated resources, not the codebase. Spacing is stated but has no scheduler. Long HTML lessons, not 2–5 minutes. | Reuse the vocabulary (fluency vs. storage strength) and the quiz-fairness rules if any probe becomes multiple choice. Don't adopt the workspace layout. |
| Explanatory / Learning `Insight` blocks | Explain the codebase while working. | Telling, not testing (illusion of explanatory depth). | Leave them alone. Checkpoint is the retrieval counterpart. |
| `eng-log` (user skill) | End-of-day reflective interview grounded in `git log` the user grants. One question at a time. | Reflection on activity, not prediction accuracy. No scoring. | Reuse the end-of-session trigger and the "ground everything in evidence, say so when the record is thin" rule. Possible later integration point. |
| `MP/engineering/retro` | End-of-session retrospective. | Improves the agent's **environment**, not the human's Theory. | No overlap in purpose. Don't confuse the two in docs. |

## Overlap: teach-back (naive teammate)

| Existing thing | What it does | What it lacks vs. this collection | Reuse recommendation |
|---|---|---|---|
| `rubber-duck` (user skill) | Socratic listening. Open questions, reflects reasoning back, names contradictions. No solutions, no hints smuggled into questions, no grading, until the user releases it. On release it hands the user's journey back first, gives its view second, and offers to capture third. | Only on explicit invitation. Never checks explanations against code. Doesn't push toward a "bedrock" constraint or trade-off. Nothing persists. | The strongest model for Principle 1. Reuse the no-smuggled-hints wording and the release order (their reasoning first, then Claude's) for teach-back and for design-first's reveal. |
| `learn` (Anthropic skill) | Tutoring: diagnose first, one question plus one scaffold per turn, "reflective pause" (explain it back, predict a change), stop once they can explain it back. | General concepts, not a system's Theory. "Holding the line under pressure" refuses the answer to an impatient learner, which **contradicts Principle 2** (escape hatch honored immediately). | Reuse "one question per turn, keep turns short" and "praise only when earned" (fits Principle 10). Explicitly diverge on pushback: skip always wins. |
| `MP/productivity/grilling` | Relentless interview. | Claude proposes the answers. Teach-back needs Claude to play the one who doesn't know. | Don't reuse. |

## Overlap: revisit (spaced resurfacing)

| Existing thing | What it does | What it lacks vs. this collection | Reuse recommendation |
|---|---|---|---|
| `MP/productivity/teach` learning records | `./learning-records/0001-slug.md`, 1–3 sentences each: understanding shown, prior knowledge stated, or a misconception corrected. Optional `Evidence`. `Status: superseded by LR-NNNN`. Used to estimate the zone of proximal development. Rule: "coverage is not learning, wait for evidence". | No due dates, no boxes, no resurfacing. Records are read at session start, not quizzed. | Reuse the evidence rule (record accuracy only from a checked answer) and supersession. Missed predictions also count as "misconception corrected", which the doc calls high-value. |
| Anything else installed | — | No installed skill or output style schedules retrieval over time. | `revisit` is new ground. Build it. |

## Overlap: decision records

| Existing thing | What it does | What it lacks vs. this collection | Reuse recommendation |
|---|---|---|---|
| `MP/engineering/domain-modeling/ADR-FORMAT.md` | `docs/adr/0001-slug.md`, a title plus 1–3 sentences (context, decision, why). Optional `Status` (`proposed / accepted / deprecated / superseded by ADR-NNNN`), `Considered Options`, `Consequences`. The directory is created lazily. Number = highest existing + 1. | Written for future readers, not for retrieval. No prediction to revisit, no reversibility field, no both-sketches field. | Make `.design-model/decisions/` match this format: same numbering, lazy creation, 1–3 sentence core, Status and supersession. Add only the extra fields (reversibility, prediction, sketches) as optional sections. Fits Principle 8 (terse). |
| ADR "when to offer" gate | Offer only if all three hold: hard to reverse, surprising without context, the result of a real trade-off. | — | When a design-first decision also passes this gate, point to or offer a real ADR rather than duplicate it. The design-model record stays the retrieval copy. |
| `MP/engineering/wayfinder` | The map is "an index, not a store": each decision lives in exactly one place. Refer to things by name, not id. | — | Apply the one-home rule to decisions vs. review-queue items: the queue item points to (`answer_ref`) the decision file and doesn't copy it. |
| `MP/engineering/pr` | PR body includes `Door: one-way / two-way` and a blast-radius note. | — | Same reversibility and blast-radius terms as the brief. Use "one-way / two-way door" wording in the rubric for consistency. |

## Overlap: triage (reversibility × blast radius × novelty)

| Existing thing | What it does | What it lacks vs. this collection | Reuse recommendation |
|---|---|---|---|
| ADR three-criteria gate | Hard to reverse, surprising, real trade-off. | No blast radius as its own axis. No novelty to this developer. A yes/no gate, not a score. | "Real trade-off (genuine alternatives)" is a useful precondition: no alternatives means no design-first. Consider it as a gate before scoring. |
| `MP/engineering/pr` door + blast radius | Two of the three axes, used for PR risk. | No novelty. | Share the terms (see above). |
| Learning output style | Picks "real design decisions" (error handling, data structures, business logic with more than one valid approach) by Claude's judgment. | No rubric, no novelty, no learner history. | Its examples are a useful starting list of decision types for the profile. |
| `MP/engineering/triage` | Issue and PR state machine (`needs-triage`, `ready-for-agent`, ...). | Unrelated concept. | **Name collision.** Call ours "triage rubric" in docs, never a `/triage` command. Update the GLOSSARY with an `_Avoid_` note if the term is added. |

## Cross-cutting conventions to reuse

| Convention | Source | Recommendation |
|---|---|---|
| `GLOSSARY.md` format (`**Term**:` definition, `_Avoid_:` aliases, no implementation details) | `MP/engineering/domain-modeling/GLOSSARY-FORMAT.md` | Already in use in this repo. Keep it. Skills that name design concepts (Skill formation) can use the same shape. |
| `disable-model-invocation: true` for user-invoked skills | `teach`, `wayfinder`, `triage`, `grill-me`, `retro` frontmatter | Matches brief §8 (developer-invoked). Check the field against the current skills docs before scaffolding. |
| Lazy file and directory creation | ADR, learning-record, and glossary formats | Create `.design-model/` pieces on first write only. |
| Deep-module vocabulary (module, interface, depth, seam, adapter, leverage, locality) | `MP/engineering/codebase-design` | Use these terms in reveals so the two collections agree. Note: it explicitly **rejects** Ousterhout's depth-as-line-ratio in favor of depth-as-leverage. Follow that if the brief's Ousterhout material is used. |
| Plugin output styles (`output-styles/` dir, `force-for-plugin`) | Output styles docs | A plugin *can* ship a style. Don't use `force-for-plugin`: it overrides the user's `outputStyle` and breaks autonomy (SDT). Not needed for Milestone 1. |
| Output style vs. skill split | Output styles docs ("Choose between an output style and other features") | Docs: a style shapes every response, a skill loads per task. Our behaviors are per moment, so they stay skills. This answers brief §11: **complement**. Each SKILL.md states that its withhold rule overrides any active style's Insight blocks until the reveal. |
