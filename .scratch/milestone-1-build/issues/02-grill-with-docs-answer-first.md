# 02: grill-with-docs-answer-first

**What to build:** A developer runs `/grill-with-docs-answer-first <feature>` and gets the same answer-first grill as `/grill-me-answer-first`, while ADRs and `GLOSSARY.md` entries are written exactly as Matt Pocock's `domain-modeling` writes them. The Decisions block then feeds `/to-spec` cleanly.

Sources: as for ticket 01; mirrors `grill-me` / `grill-with-docs`.

**Blocked by:** 01 (grill-me-answer-first, installable end to end)

**Status:** ready-for-agent

- [x] `grill-with-docs-answer-first` is user-invoked only, loads `grilling` and `domain-modeling`, reads the same shared grill procedure as ticket 01 (no copy of it), and says what to do if either dependency is missing.
- [x] `claude plugin validate --strict` passes.
- [ ] Human check: a real session produces at least one ADR or glossary entry in `domain-modeling`'s format.
- [ ] Human check: running `/to-spec` after the session yields a spec whose decisions match the Decisions block, with no revised Commitments, rejected recommendations, or critique text recorded as decisions.
- [ ] Human check: Lock-in against the recommendation produces exactly one objection, then acceptance (not observed during prototyping).

## Implementation notes

- **Skill:** `plugins/learning-skills/skills/grill-with-docs-answer-first/SKILL.md` is the prototype's, with the missing-dependency message worded as in ticket 01 (install command included). It reads `${CLAUDE_PLUGIN_ROOT}/shared/answer-first-grill.md` and copies none of it; the shared procedure says nothing about ADRs or the glossary, so `domain-modeling` governs their format unopposed.
- **README:** one Commands entry added; the Dependency line now names `domain-modeling` too.
- **Checks run:** `claude plugin validate --strict plugins/learning-skills` and `claude plugin validate --strict .` both pass, plus grep checks for each part of the first box (frontmatter, both skill names, shared-procedure path, install command, README entry).
