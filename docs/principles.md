# Principles

Every skill in the `learning-skills` plugin follows these rules. They come from the project brief (`planning_doc.md` §5), updated for the decisions in `.scratch/milestone-1-spec/map.md`. Vocabulary is in `GLOSSARY.md`.

The goal is that the developer keeps the **Theory** of their system: they do the design thinking, and Claude's view arrives only after theirs.

1. **Withhold, then reveal.** When a skill asks for the developer's view, Claude gives no answer, hint, ranking, or leaning until the developer has made a **Commitment**. Each SKILL.md or the procedure it reads says so explicitly, because Claude's default helpfulness otherwise undoes the exercise. Hold back output-style Insight or explanation blocks too, until the reveal (in the answer-first grill, the critique step).
2. **Skip always wins.** *skip*, *just tell me*, or *not now* is honoured at once, with no comment, argument, or guilt. The developer's depth override (*open*, *pick one*, *just tell me*) beats Claude's triage.
3. **Triage before friction.** Hold back only on decisions that matter: costly to undo (reversibility) or touching several **Areas** (blast radius), weighted by how new the call is to this developer. The rule is short and lives in the procedure that uses it (`plugins/learning-skills/shared/answer-first-grill.md`). Triage sets **Depth**; it never decides whether a skill runs. Judge from what is already known; never read code just to triage.
4. **Verify against reality.** Prefer prompts whose answers can be checked against the code (Ripple predictions, per-claim verdicts). Back every claim about the codebase with `file:line`.
5. **Ask, don't lecture.** Explanations come after the developer's attempt and focus on where their view and reality diverge.
6. **Honest feedback.** When the developer is wrong, say so plainly and specifically. Flattery destroys calibration. After one stated objection, the developer's **Lock-in** is the decision.
7. **Short and bounded.** An exercise usually takes 2–5 minutes; say up front how long. Caps (two Ripple questions per session, a few teach-back threads) keep it bounded.
8. **Manual-only.** Every skill is developer-invoked: `disable-model-invocation: true` in its frontmatter. Nothing auto-fires, no hooks, no piggybacking on other skills. Because slash-only skills cannot call each other, shared logic lives in files under `${CLAUDE_PLUGIN_ROOT}/shared/` that each skill reads.
9. **Nothing is persisted.** Milestone 1 scores in the moment: no learner profile, Area registry, prediction files, or review queue. Novelty is Claude's in-session estimate, so per-Area fading waits for a later milestone. The one output is the session's Decisions block, written to `.scratch/<feature-slug>/decisions.md` only when the developer says *save*.
10. **Build on, don't fork.** The skills load Matt Pocock's `grilling` (and `domain-modeling` where docs are written) and change only what the learning loop needs. `/grill-me` and `/grill-with-docs` stay untouched.
