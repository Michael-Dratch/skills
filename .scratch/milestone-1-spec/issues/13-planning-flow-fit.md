# How design-first fits the planning flow

Type: grilling
Status: resolved
Blocked by: 

## Question

The developer plans features with `grill-me` (or `grill-with-docs`, which writes ADRs and glossary terms), then `to-spec`, then optionally `to-tickets`, and wants to invoke `design-first` alongside that flow when they want a forced design-first session. How does `design-first` fit: its entry points relative to grilling (before Claude's recommendations are visible), its hand-off into the grill and the spec, what it persists beside ADRs and the spec without duplicating them, and when and how the Change-impact drill's Areas prediction is scored given implementation happens later, often in other sessions?

## Answer

1. **`/design-first <prompt>` is a grilling session with built-in friction.**
   - **Up-front sketch:** the developer sketches the whole feature, including one ripple line: what existing code depends on what this design changes. In parallel an isolated subagent designs from the prompt alone (per [How to enforce withhold-then-reveal](03-enforce-withholding.md)).
   - **Reveal:** Claude compares the two designs and checks the ripple line against the current code (imports, callers, shared types), backing every finding with `file:line` evidence.
   - **Hand-off:** a short block in the conversation: the developer's direction in their words, plus open disagreements and ripple surprises written as questions.
   - **Grill:** the skill loads `grilling` and starts on those questions. Each grill question is triaged ([Triage rubric](06-triage-rubric.md)): full or light → Claude withholds its recommendation until the developer answers, then reveals and compares; offer-skip → normal grilling with a recommendation. "Just tell me" always works.
2. **`/design-first-with-docs`** is identical but also loads `domain-modeling`, so ADRs and `GLOSSARY.md` follow Matt Pocock's exact format. Mirrors `grill-me` / `grill-with-docs`. Both SKILL.md files share one procedure file under `${CLAUDE_PLUGIN_ROOT}`.
3. **`/grill-me` and `/grill-with-docs` are untouched** and behave normally; `/to-spec` and `/to-tickets` run as usual afterwards (`to-spec` synthesises from the conversation, so the hand-off flows into the spec).
4. **Mid-conversation use:** `/design-first <decision>` on a single decision first checks whether Claude's recommendation for it already appeared in the conversation; if so it says so plainly and offers light or skip.
5. **Dependencies:** the plugin requires Matt Pocock's `grilling` and `domain-modeling` (both model-invocable, so a skill can load them; `grill-me` itself cannot be invoked by Claude). README lists them; each skill says what to do if missing.
6. **Milestone 1 persists nothing.** All scoring happens in the moment with data available in the session. No learner profile, no Area registry, no prediction files, no session records. Novelty is Claude's in-session estimate plus the developer's override. Learning happens in the reveal; stored scores would only serve automatic fading, measurement, and cross-session blind-spot spotting, which do not justify the machinery for a dogfood milestone.
7. **Change-impact drill rescoped:** predict what *existing* code depends on the design's changes, checked in the session against the real code, as part of the up-front sketch. Not scored against a later diff; `teach-back` has no part in it.

Rejected along the way: `/design-first /grill-me` chained syntax (replaced by one command); design-first as a separate practice exercise (out of scope; skill formation only); a session record (Q4) and prediction files (superseded by item 6–7).

## Amendment (from [Answer-first grilling as its own skill](14-answer-first-grilling.md))

`design-first` and `design-first-with-docs` are deferred out of Milestone 1. Carried over into the shared grill procedure: triaged withholding, `-with-docs` via `domain-modeling`, the dependency on `grilling`, `/grill-me` untouched, persisting nothing. The Change-impact drill becomes the Ripple question inside the grill. Deferred with `design-first`: up-front sketch, isolated subagent, hand-off block, mid-conversation anchoring check.
