# Eval plan

Type: grilling
Status: open
Blocked by: 01, 15

## Question

What evals does Milestone 1 ship with: scenarios that check Claude withholds recommendations on triaged grill questions until the developer commits, shows open vs. listed questions by depth, handles both Lock-in edge cases, backs Ripple findings with `file:line` evidence and respects the two-per-session cap, emits the `🔒 Locked` lines and Decisions block, honours skip immediately, stays in its time budget, and leaves `/grill-me` behaviour unchanged; plus `teach-back` checks. Which tooling runs them (multi-turn needs `history_file`), and what passes?
