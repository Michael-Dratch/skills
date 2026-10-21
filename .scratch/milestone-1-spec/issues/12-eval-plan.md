# Eval plan

Type: grilling
Status: closed (out of scope)
Blocked by: 01, 15

## Question

What evals does Milestone 1 ship with: scenarios that check Claude withholds recommendations on triaged grill questions until the developer commits, shows open vs. listed questions by depth, handles both Lock-in edge cases, backs Ripple findings with `file:line` evidence and respects the two-per-session cap, emits the `🔒 Locked` lines and Decisions block, honours skip immediately, stays in its time budget, and leaves `/grill-me` behaviour unchanged; plus `teach-back` checks. Which tooling runs them (multi-turn needs `history_file`), and what passes?

## Closed: out of scope (deferred by the developer, 2026-10-21)

Starting point for the later effort, drafted before deferral: a ~10-case regression suite on the behaviours that run against Claude's defaults (🧠 questions carry no recommendation; high-stakes new questions asked open; routine questions stay normal; matching Lock-in is one line; Lock-in against the recommendation gets one objection; Ripple findings cite a real `file:line`; Decisions block lists only Lock-ins; "just tell me" honoured at once; teach-back *why* questions carry no hints; 🕳️ is marked, not filled or counted wrong). Pass = check met in 2 of 3 runs. Open risk found: `claude plugin eval` runs load only this plugin, so `grilling` / `domain-modeling` may be unavailable; see `research/12-eval-dependencies.md` if present.
