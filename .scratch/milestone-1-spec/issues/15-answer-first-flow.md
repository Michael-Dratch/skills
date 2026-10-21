# grill-me-answer-first flow

Type: prototype
Status: claimed
Blocked by: 14

## Question

What does a `grill-me-answer-first` session look like end to end, as decided in [Answer-first grilling as its own skill](14-answer-first-grilling.md)? Prototype the shared grill procedure plus the `grill-me-answer-first` and `grill-with-docs-answer-first` SKILL.md files and run them on a real feature: the opening depth line, per-question triage (open / listed / normal), Commitment, critique, re-display and Lock-in including both edge cases, Ripple questions with `file:line` checks and the two-per-session cap, the `🔒 Locked` / `⚠️ Must handle` lines and final Decisions block, "save" to `decisions.md`, the escape hatch, and the time budget. Key risks to test: whether Claude reliably withholds despite `grilling`'s instruction to always recommend, and whether `/to-spec` produces a clean spec from the Decisions block.

## Progress

- Prototype drafted at `prototype/answer-first-PROTOTYPE/` (throwaway plugin; `claude plugin validate` passes). Run with `claude --plugin-dir <that path>`.
- Decided before the run (developer, 2026-10-21): the triage rubric is compressed into a short rule inside the shared procedure, with "never read code just to triage" and no score reveal; the rubric file is gone. Recorded as an amendment on [Triage rubric](06-triage-rubric.md).
- Waiting on: the developer's real-feature run and notes on withholding, depth choices, critique quality, Lock-in, Ripple questions, feel, and `/to-spec` output.
