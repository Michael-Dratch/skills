# 03: teach-back

**What to build:** A developer runs `/teach-back [feature, files, or branch]` on a finished feature: rates their understanding, explains it to Claude playing a new teammate who asks *why* without hints until each thread reaches a constraint, a tradeoff, or 🕳️ no theory, re-rates, then sees a reveal with a calibration line, per-claim verdicts (✅ ◐ ❌ ❔) checked against the code, ADRs, spec and `decisions.md`, 🕳️ findings routed to `/grill-me-answer-first` or an ADR, ⚠️ drift findings, and the biggest gaps.

Sources: the map's tickets "What counts as the real answer" and "teach-back flow", and the prototype's teach-back skill.

**Blocked by:** 01 (grill-me-answer-first, installable end to end)

**Status:** ready-for-agent

- [x] `teach-back` is user-invoked only and ported from the prototype: feature from the argument or the branch diff against its merge base (else ask in one line); 2–3 threads, at most four *why*s each; "about 5 minutes" stated up front; *skip* ends a thread (marked skipped), *stop* jumps to the reveal.
- [x] Verdicts cover at most about 10 load-bearing claims; the code wins for mechanism claims; records that contradict the code produce a separate drift finding; nothing is persisted.
- [x] `claude plugin validate --strict` passes.
- [ ] Human check: a real run on a finished feature gives verdicts whose `file:line` references resolve, and at least one rationale claim is checked against an ADR, spec, or `decisions.md`.

## Implementation notes

- **Skill:** `plugins/learning-skills/skills/teach-back/SKILL.md`, copied verbatim from the prototype; it already met every agent-checkable criterion, so no wording changed. It reads no shared procedure and does not load `grilling`, so it needs no missing-dependency message.
- **README:** one `/teach-back` line added under Commands.
- **Checks run:** `claude plugin validate --strict plugins/learning-skills` and `claude plugin validate --strict .` both pass, plus grep checks for each agent-checkable box (user-invoked only, merge-base default, ask in one line, 2–3 threads, four *why*s, about 5 minutes, skip/stop, at most 10 verdicts, code wins, drift, ✅ ◐ ❌ ❔ 🕳️, nothing saved).
