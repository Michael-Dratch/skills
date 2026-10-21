# What counts as the real answer

Type: grilling
Status: resolved
Blocked by: 

## Question

What is ground truth for `teach-back`: how is each claim in the developer's explanation checked in the session rather than accepted on their word? Mechanism claims against the code; rationale claims against the project's ADRs, the feature's spec, and code evidence of the constraint. What verdicts exist (right / wrong / partly / unverified), and how are they shown? Nothing is persisted (per [How design-first fits the planning flow](13-planning-flow-fit.md)).

## Answer

1. **Verdicts per claim.** Mechanism claims are checked against the code: ✅ right, ◐ partly right, ❌ wrong, each with `file:line`. Rationale claims are checked first against an ADR or the feature's spec or `decisions.md`, then against code evidence of the constraint; ❔ unverified when nothing shows the answer (Claude never invents one).
2. **Timing (withhold, then reveal):** rate (1–7) → explain (naive-teammate "why" with no corrections and no hints) → re-rate, still before any verdict → verdicts → discussion of the two or three biggest gaps. The reveal opens with a calibration line, e.g. "You rated 6, then 4; 5 of 8 load-bearing claims were right." Shown, never saved. Order follows `rubber-duck` (developer's reasoning first) and Rozenblit & Keil (re-rate before feedback exposes the illusion of explanatory depth).
3. **🕳️ no theory** for "I don't know" / "that's how Claude wrote it": not counted wrong. Claude does not fill the gap during questioning. At the reveal, Claude gives the reason with its source if an ADR, spec, or the code shows it; otherwise it says plainly that nobody knows and points to `/grill-me-answer-first` (decision still worth thinking about) or an ADR (once the reason is known).
4. **Conflicting sources:** the code wins for mechanism claims. A record contradicting the code becomes a separate ⚠️ **drift** finding; a claim that matched the stale record is ❌, with the drift note explaining why.
5. **Checking budget:** Claude silently reads the feature's code up front (files the developer names, or the branch's diff), which also feeds its questions. Only load-bearing claims (those the explanation of *why* depends on) get a verdict, at most about 10 per session, preferring those closest to a constraint or tradeoff. ADRs, spec and `decisions.md` are read only when a rationale claim needs them. Nothing is persisted.

Left for [teach-back flow](11-teach-back-flow.md): choosing the feature, what counts as bottoming out, number and depth of threads, time budget.
