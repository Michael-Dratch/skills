# grill-me-answer-first flow

Type: prototype
Status: resolved
Blocked by: 14

## Question

What does a `grill-me-answer-first` session look like end to end, as decided in [Answer-first grilling as its own skill](14-answer-first-grilling.md)? Prototype the shared grill procedure plus the `grill-me-answer-first` and `grill-with-docs-answer-first` SKILL.md files and run them on a real feature: the opening depth line, per-question triage (open / listed / normal), Commitment, critique, re-display and Lock-in including both edge cases, Ripple questions with `file:line` checks and the two-per-session cap, the `🔒 Locked` / `⚠️ Must handle` lines and final Decisions block, "save" to `decisions.md`, the escape hatch, and the time budget. Key risks to test: whether Claude reliably withholds despite `grilling`'s instruction to always recommend, and whether `/to-spec` produces a clean spec from the Decisions block.

## Progress

- Prototype drafted at `prototype/answer-first-PROTOTYPE/` (throwaway plugin; `claude plugin validate` passes). Run with `claude --plugin-dir <that path>`.
- Decided before the run (developer, 2026-10-21): the triage rubric is compressed into a short rule inside the shared procedure, with "never read code just to triage" and no score reveal; the rubric file is gone. Recorded as an amendment on [Triage rubric](06-triage-rubric.md).
- Waiting on: the developer's real-feature run and notes on withholding, depth choices, critique quality, Lock-in, Ripple questions, feel, and `/to-spec` output.

## Answer

**Verdict: the answer-first loop works as designed; build it.** Prototype asset: [`prototype/answer-first-PROTOTYPE/`](../../../prototype/answer-first-PROTOTYPE/) (shared procedure `shared/answer-first-grill.md` plus the two SKILL.md files). It is the starting point for the real skills.

Developer's run on a real feature in another repo (2026-10-21):
- **Triage worked.** Most held-back questions came as 🧠 pick-one (options, no recommendation); one larger question came as 🧠 open with no options.
- **Critique:** liked.
- **`🔒 Locked` formatting:** liked.
- **Ripple question:** fired once; the check of the developer's prediction was strong.
- Ready to move forward; may tune later from more use.

Not yet observed (carry into dogfooding and the [Eval plan](12-eval-plan.md)): whether *normal* questions appeared often enough (most held-back questions being pick-one may mean the rule is a little loose), the Lock-in-against-recommendation edge case, the `grill-with-docs-answer-first` variant, the Decisions block feeding `/to-spec`, "save" to `decisions.md`, and the time per question.
