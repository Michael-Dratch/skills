# Map: Milestone 1 spec for learning-first skills

Label: wayfinder:map

## Destination

A buildable spec for Milestone 1: the shared core (principles, triage rubric, shared grill procedure) plus `grill-me-answer-first` and `grill-with-docs-answer-first` (grilling with withheld recommendations, Lock-in, and Ripple questions) and `teach-back`, all developer-invoked and persisting nothing. Specified sharply enough that implementation involves no further decisions. Audience is the author first, packaged as an installable plugin; builds on Matt Pocock's `grilling` and `domain-modeling`, complements the rest; protecting the developer's **Theory** of their system is the primary outcome.

## Notes

- Founding brief: `planning_doc.md` (repo root). Vocabulary: `GLOSSARY.md`.
- Settled during charting: destination is Milestone 1 only, with `teach-back` added; audience is the author first (team use out of scope); Theory (system-specific) is primary and Skill formation secondary; position is complementary to Matt Pocock's skills and Claude Code output styles (later amended: the plugin builds on `grilling` and `domain-modeling`, per [How design-first fits the planning flow](issues/13-planning-flow-fit.md)).
- Skills to consult: `grilling` + `domain-modeling` for grilling tickets; `prototype` for prototype tickets; `research` for research tickets; `writing-for-agents` whenever drafting SKILL.md text.
- Stores nothing: Milestone 1 scores in the moment; no learner profile, Area registry, or prediction files.
- Manual-only: every Milestone 1 skill is developer-invoked (`disable-model-invocation: true`); nothing auto-fires or piggybacks.
- **Map complete (2026-10-21); built:** the Milestone 1 skills live in the `learning-skills` plugin at `plugins/learning-skills/` (marketplace at the repo root); build tickets in `.scratch/milestone-1-build/issues/`. The throwaway prototype referenced by closed tickets has been removed; its content was ported into the plugin.
- Standing preference: ask the human one question at a time, not batched rounds.
- No git repo yet: research findings go to `.scratch/milestone-1-spec/research/`, not branches.

## Decisions so far

- [Overlap with existing skills and output styles](issues/02-overlap-existing-skills.md): nothing installed does commit-first + code-verified + spaced work; complement output styles (suppress Insight blocks until the reveal), reuse ADR/glossary/numbering formats and rubber-duck's withhold wording, avoid the name `/triage`; `revisit` is new ground
- [How to enforce withhold-then-reveal](issues/03-enforce-withholding.md): order, not instructions: background isolated subagent designs from the task alone (returns only a file path) before the sketch exists, the developer sketches meanwhile, then reveal; plugin PreToolUse hook gating the sketch path is a best-effort backstop; a sketch already in chat can't be un-seen
- [Current Claude Code plugin and skill format](issues/01-plugin-skill-format.md): one repo = marketplace + plugin (as built: plugin in `plugins/learning-skills/`, marketplace at the repo root, because a root `CLAUDE.md` fails `validate --strict`; see build ticket 01 notes); slash-only via `disable-model-invocation: true` (also blocks skill-to-skill and subagent preload, so share logic via files under `${CLAUDE_PLUGIN_ROOT}`); state in `${CLAUDE_PLUGIN_DATA}` or project; non-fork subagents get no chat history; `claude plugin eval` (GA) does with/without-plugin evals, `history_file` for multi-turn
- [What is an Area of the system](issues/04-define-area.md): a part of the system named in the developer's terms; decision type is a tag, not an Area (registry, drift, splits, overlap rules later made moot by storing nothing; amended)
- [Milestone 1 lineup and build order](issues/05-milestone-lineup.md): manual-only; `design-first` + `design-first-with-docs`, then `teach-back` (absorbs rate-explain-re-rate); `change-impact` and `/revisit` removed (amended three times); vertical slices, dogfooding each; later amended to the answer-first pair → `teach-back`
- [Triage rubric](issues/06-triage-rubric.md): sets depth (full/light/offer skip) and picks targets, never whether to run; originally a 3-level stakes × novelty grid, later compressed to a short in-procedure rule with no code reading (amended); scored silently, revealed after; novelty estimated in-session (amended); silent no-alternatives gate in `design-first`; also triages each grill question for withholding
- [How design-first fits the planning flow](issues/13-planning-flow-fit.md): `/design-first` is a grilling session with friction: up-front sketch + ripple line vs isolated subagent design, reveal with `file:line`-backed ripple check, hand-off questions, then `grilling` withholding recommendations on triaged questions; `-with-docs` twin adds `domain-modeling`; `/grill-me` untouched; requires `grilling` + `domain-modeling`; Milestone 1 persists nothing
- [Answer-first grilling as its own skill](issues/14-answer-first-grilling.md): `grill-me-answer-first` + `grill-with-docs-answer-first` share one grill procedure: triaged questions (full open / light listed / normal), Commitment → critique → re-display → Lock-in (objection once, developer has last word), up to two Ripple questions with `file:line` checks, `🔒 Locked` lines + final Decisions block for `to-spec`; `design-first` deferred
- [grill-me-answer-first flow](issues/15-answer-first-flow.md): prototype run on a real feature worked: triage split pick-one vs open correctly, critique and `🔒 Locked` lines liked, Ripple question strong; the prototype was the starting point and has been ported into the plugin; `/to-spec` hand-off, lock-against edge case and `-with-docs` still unobserved
- [What counts as the real answer](issues/07-ground-truth.md): `teach-back` gives per-claim verdicts (✅ ◐ ❌ against code with `file:line`; rationale against ADR/spec/`decisions.md` then code; ❔ unverified); rate → explain → re-rate → verdicts with a calibration line; 🕳️ no theory routed to answer-first grill or an ADR; code wins, records that disagree flagged as ⚠️ drift; read feature code up front, ≤10 load-bearing verdicts
- [teach-back flow](issues/11-teach-back-flow.md): prototype worked on a real feature; adopt the draft: feature from argument or branch diff, threads end at constraint / tradeoff / 🕳️, 2–3 threads × ≤4 whys, ~5 min, skip/stop escape hatch; prototype SKILL.md is the starting point

## Not yet specified

Nothing. The last two patches were folded into the spec as defaults (developer, 2026-10-21):
- **Dogfood protocol:** use on current projects; one-line note per session (helped / annoyed / skipped); review after about two weeks or ten sessions.
- **README and install:** declare `mattpocock-skills` in plugin.json `dependencies` (cross-marketplace, so `marketplace.json` needs `allowCrossMarketplaceDependenciesOn`; a missing dependency disables the plugin), verified on first real install; README lists the dependency and the three commands. See `research/12-eval-dependencies.md`.

## Out of scope

- Remaining catalog skills (`decision-frame`, `counterfactual`, `review-before-accept`, `hypothesis-debug`, `new-territory`, `design-critique`): later efforts once dogfooding shows what matters.
- Team / shared theory (committed shared state, multiple contributors): audience is the author first.
- `/revisit` and spaced resurfacing (review queue, Leitner scheduler): removed from Milestone 1 by the developer; [Revisit mechanics](issues/09-revisit-mechanics.md) closed.
- Cross-project Skill formation profile: secondary outcome; revisit after Milestone 1.
- Automatic triggering of any kind (`revisit` piggybacking on other skills, hooks, model-invoked skills): Milestone 1 is manual-only, per [Milestone 1 lineup and build order](issues/05-milestone-lineup.md).
- `design-first` and `design-first-with-docs` (up-front whole-feature sketch, isolated subagent design, hand-off block): deferred by the developer; [design-first flow](issues/10-design-first-flow.md) closed. Decisions in [How design-first fits the planning flow](issues/13-planning-flow-fit.md) remain for a later effort.
- Evals ([Eval plan](issues/12-eval-plan.md) closed): deferred by the developer; a ~10-case regression suite is drafted in the ticket for a later effort. Until then, dogfooding is the only check on withholding when SKILL.md files are tuned.
- Persisted scoring: learner profile, Area registry, automatic fading, calibration trends, cross-session blind-spot detection; [State schema](issues/08-state-schema.md) closed. A Milestone 2 effort if dogfooding shows the need.
- Import-graph "map from memory" probe and its extraction scripts; hooks; multi-language support: deferred by the brief.
