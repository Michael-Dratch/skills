# teach-back flow

Type: prototype
Status: resolved
Blocked by: 07

## Question

What does a `teach-back` session look like end to end? Prototype a rough SKILL.md and run it on a real feature: the opening and closing "rate, explain, re-rate" (explanation-depth probe), choosing what to probe via the triage rubric, the naive-teammate questioning, when an explanation has bottomed out, checking claims against the code, ADRs and spec, flagging "that's how Claude wrote it", the escape hatch, and the time budget. Verdicts, 🕳️ handling, drift, and the checking budget are already decided in [What counts as the real answer](07-ground-truth.md). Nothing is persisted.

## Answer

**Verdict: the drafted flow works; build it as drafted.** Prototype asset: [`prototype/answer-first-PROTOTYPE/skills/teach-back/SKILL.md`](../../../prototype/answer-first-PROTOTYPE/skills/teach-back/SKILL.md), the starting point for the real skill. The developer ran it on a recent real feature (2026-10-21) and reported it worked well.

Flow defaults adopted (from the draft, accepted by the run):
- **Feature choice:** `/teach-back <files, feature, or branch>`; empty → current branch diff against its merge base with main; none → ask in one line.
- **Bottoming out:** a thread ends at a **constraint** (requirement, external API/platform limit, business rule, deadline), a **tradeoff** (options named and what was given up for what), or **🕳️ no theory**.
- **Threads:** 2–3, chosen silently from the feature's costly-to-undo or cross-cutting decisions; at most four *why*s each.
- **Time budget:** "about 5 minutes", stated up front. *Skip* ends a thread (marked skipped, not 🕳️); *stop* jumps to the reveal.
- Verdicts, 🕳️ handling, drift, checking budget: as in [What counts as the real answer](07-ground-truth.md).

No detailed observations were reported, so these stay unverified and carry into dogfooding and the [Eval plan](12-eval-plan.md): neutrality of the *why* questions, bottoming-out timing, thread choice, verdict accuracy and the 10-verdict cap, 🕳️ and drift routing, the calibration line, and actual duration.
