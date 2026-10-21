# Answer-first grilling as its own skill

Type: grilling
Status: resolved
Blocked by: 

## Question

Should Milestone 1 include a lighter skill that is `grill-me` with friction: for significant questions (per the triage rubric), withhold the recommendation, take the developer's answer, critique it and discuss the tradeoffs, then re-display the question with the recommendation for the developer to lock in? How does it relate to `design-first` (which runs an up-front sketch and then a withholding grill), what is it called, does it get a `-with-docs` twin, and how does the lock-in step change `design-first`'s grill phase and the build order?

## Answer

1. **New skills:** `grill-me-answer-first` and `grill-with-docs-answer-first` (the latter also loads `domain-modeling` for ADRs and `GLOSSARY.md`). Both load `grilling` and follow one **shared grill procedure** file under `${CLAUDE_PLUGIN_ROOT}`. `/grill-me` and `/grill-with-docs` stay untouched.
2. **The loop:** each grill question is triaged ([Triage rubric](06-triage-rubric.md)).
   - **Full:** open question, no options, no recommendation.
   - **Light:** options listed, no recommendation.
   - **Offer-skip:** normal `grill-me` question with options and recommendation.
   For full and light: the developer's **Commitment** → honest critique and tradeoffs (naming the design concept) → question re-displayed with options and recommendation → the developer's **Lock-in**.
3. **Lock-in edge cases:** a Commitment matching the recommendation is confirmed in one line ("Same as my recommendation. Locked in unless you want to change it."), still naming one tradeoff; no re-display. A Lock-in against the recommendation gets Claude's strongest objection once, in a sentence, then is accepted without further argument and recorded as the developer's decision.
4. **Ripple question** (the former Change-impact drill): asked as a separate question right after a Lock-in that changes something other code relies on (interface, data shape, shared type, contract) whose depth was full or light. Open, no hints. Claude checks the prediction against the current code with `file:line` per finding (missed / not affected / right). On a surprise that could change the decision, Claude asks once whether it changes the Lock-in. At most two per session, chosen by the triage grid.
5. **`design-first` and `design-first-with-docs` are deferred** (out of scope): the up-front sketch, isolated subagent design, hidden design file, and hand-off block go with them.
6. **Milestone 1:** shared core (principles, triage rubric, shared grill procedure), `grill-me-answer-first` + `grill-with-docs-answer-first`, `teach-back`. Build order: the answer-first pair, then `teach-back`.
7. **Record for `to-spec`:** after each Lock-in a fixed-format line `🔒 Locked: <decision>. <reason in the developer's words>`; ripple findings needing handling as `⚠️ Must handle: …`. The session ends with a **Decisions** block labelled as the authoritative list for the spec. On "save", the block is written to `.scratch/<feature>/decisions.md` for a spec written in a later session. This guards against `to-spec` picking up revised Commitments, rejected recommendations, or critique text, and against compaction blurring which answer was final.
