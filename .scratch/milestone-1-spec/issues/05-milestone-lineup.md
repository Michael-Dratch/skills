# Milestone 1 lineup and build order

Type: grilling
Status: resolved
Blocked by: 01

## Question

Confirm the Milestone 1 set (`design-first`, `checkpoint` (now `change-impact`), `teach-back`, minimal `revisit`, shared core) works as a set, decide which `checkpoint` probes are included, and fix the build order.

## Answer

1. **Manual-only.** Milestone 1 ships only developer-invoked skills: nothing auto-fires, piggybacks on another skill, or is model-invoked. Every skill sets `disable-model-invocation: true`.
2. **Lineup:** shared core (principles, triage rubric, state schema), `design-first`, `change-impact` (renamed from `checkpoint`: the old name no longer described one probe, and collides with Claude Code's own checkpoint/`/rewind` feature), `teach-back`, and `/revisit`.
3. **`/revisit` is manual.** It keeps a due-date (Leitner) schedule so it knows what's due, but never surfaces items on its own. Kept because it is the set's only use of the spacing effect and gives persisted predictions a reader.
4. **Probes:** `change-impact` runs the Change-impact drill only. The explanation-depth probe ("rate, explain, re-rate") moves into `teach-back` as its opening and closing, giving it calibration data. Map-from-memory stays out of scope.
5. **Build order:** vertical slices, each dogfooded before the next: `design-first` → `change-impact` → `teach-back` → `/revisit`. Build only as much shared core as each slice needs. `design-first` goes first as highest priority and riskiest (hidden-design withholding); `/revisit` last since it needs weeks of saved items.

## Amendment (from [Triage rubric](06-triage-rubric.md))

`change-impact` is removed as a separate skill. The Change-impact drill moves into `design-first`: the developer's sketch includes the Areas the change will touch, and the real diff after implementation scores it per Area. Reasons: a hypothetical requirement has no real answer (Claude's trace is only another prediction), it felt like a quiz detached from real work, and it ran at the least engaging moment. Revised lineup: shared core, `design-first`, `teach-back`, `/revisit`. Revised build order: `design-first` → `teach-back` → `/revisit`.

## Amendment 2

`/revisit` is removed from Milestone 1 by the developer's decision; [Revisit mechanics](09-revisit-mechanics.md) is closed as out of scope. Revised lineup: shared core, `design-first`, `teach-back`. Revised build order: `design-first` → `teach-back`.

## Amendment 3 (from [How design-first fits the planning flow](13-planning-flow-fit.md))

Lineup: shared core (principles, triage rubric; no state schema), `design-first` (a grilling session with built-in friction, including the rescoped Change-impact drill), `design-first-with-docs` (same plus `domain-modeling`), `teach-back`. Build order: `design-first` (with its `-with-docs` twin) → `teach-back`.

## Amendment 4 (from [Answer-first grilling as its own skill](14-answer-first-grilling.md))

Lineup: shared core (principles, triage rubric, shared grill procedure), `grill-me-answer-first` + `grill-with-docs-answer-first`, `teach-back`. `design-first` and its twin are deferred. Build order: the answer-first pair → `teach-back`.
