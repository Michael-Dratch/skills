# 01: grill-me-answer-first, installable end to end

**What to build:** A developer installs the `learning-skills` plugin from this repo's marketplace into a real project and runs `/grill-me-answer-first <feature>`: a normal grilling session, except significant questions withhold Claude's recommendation, take the developer's Commitment, critique it, re-display with the recommendation, and record the Lock-in; up to two Ripple questions check dependency predictions against the code; the session ends with a Decisions block. This slice turns the repo into the marketplace and plugin and ships the shared grill procedure the later skills reuse.

Sources: the wayfinder map `.scratch/milestone-1-spec/map.md` (Decisions so far, Notes), its tickets "Answer-first grilling as its own skill", "grill-me-answer-first flow" and "Triage rubric" (amended short rule), the prototype `prototype/answer-first-PROTOTYPE/`, and `GLOSSARY.md`.

**Blocked by:** None (can start immediately)

**Status:** ready-for-agent

- [x] The repo is both the marketplace and the single plugin `learning-skills` (marketplace entry with `"source": "."`), per the plugin-format research.
- [x] The plugin declares `mattpocock-skills` as a dependency, with the cross-marketplace allowance in the marketplace manifest; if this cannot work, the README states the dependency instead and the ticket notes why.
- [x] The shared grill procedure is ported from the prototype, including the short triage rule ("never read code just to triage"), the 🧠 open / 🧠 pick one / normal question forms, Commitment → critique → re-display → Lock-in with both edge cases, Ripple questions (max two, `file:line` per finding), `🔒 Locked` / `⚠️ Must handle` lines, the final Decisions block, and "save" to `decisions.md`.
- [x] `grill-me-answer-first` is user-invoked only, loads `grilling`, reads the shared procedure (which wins where it differs), and says what to do if `grilling` is missing.
- [x] A principles document holds the brief's design principles, updated for the map's decisions: manual-only skills, nothing persisted, skip always wins, holding back Insight blocks until the reveal.
- [x] The README gives install steps, the dependency, the available commands, and the dogfood protocol: use on current projects, a one-line note per session (helped / annoyed / skipped), review after about two weeks or ten sessions.
- [x] `claude plugin validate --strict` passes.
- [ ] Human check: install from the marketplace in a real project, run `/grill-me-answer-first` on a real feature, and confirm 🧠 questions carry no recommendation and `/grill-me` still behaves normally.

## Implementation notes

- **Layout:** marketplace at the repo root (`.claude-plugin/marketplace.json`, name `my-skills`), plugin in `plugins/learning-skills/` (entry `"source": "./plugins/learning-skills"`, not `"."`). Root-as-plugin was tried first: `claude plugin validate --strict .` (Claude Code 2.1.303) fails on one warning, "CLAUDE.md at the plugin root is not loaded as project context". The subdirectory keeps `CLAUDE.md`, `GLOSSARY.md`, `docs/`, `.scratch/`, `planning_doc.md` and `prototype/` out of the installed plugin cache and passes `--strict`. The first box is ticked on that basis.
- **Shared procedure:** `plugins/learning-skills/shared/answer-first-grill.md`, copied verbatim from the prototype. `skills/grill-me-answer-first/SKILL.md` is the prototype's, with the install command added to the missing-`grilling` message.
- **Principles:** `docs/principles.md` at the repo root (an authoring document, not read by the skills at runtime).
- **Dependency:** `plugin.json` `dependencies: [{ "name": "mattpocock-skills", "marketplace": "claude-plugins-official" }]`, plus `allowCrossMarketplaceDependenciesOn: ["claude-plugins-official"]` in `marketplace.json`. `validate --strict` accepts both (plugin and marketplace). Not yet verified by a real install; the README states the dependency and the manual install command as a fallback. Per `research/12-eval-dependencies.md`, the declared dependency may disable the plugin under `claude plugin eval`; revisit when evals are built.
- **Checks run:** `claude plugin validate --strict plugins/learning-skills` and `claude plugin validate --strict .` both pass, plus grep checks for each agent-checkable box.
