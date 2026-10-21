# Triage rubric

Type: grilling
Status: resolved
Blocked by: 04

## Question

What is the shared triage rubric: scales for reversibility, blast radius, and novelty; how they combine; the threshold for deep engagement vs. a one-line note vs. nothing; who scores (Claude proposes, developer confirms?); and how novelty is estimated on a cold start with no history?

## Answer

1. **Job:** sets **depth** (full / light / offer to skip) and **picks targets** when the developer names none. It never decides whether a skill runs (Milestone 1 is manual-only); the developer's one-word override ("full", "light", "skip") always wins.
2. **Scales:** three ordinal levels per axis with anchors.
   - **Reversibility:** two-way door (undone in under an hour, nothing else breaks) / costly (a day or more, or a migration) / one-way door (data shape, public API, external contract). Wording matches the `pr` skill.
   - **Blast radius:** 1 Area / 2–3 Areas / 4+ Areas or code everything shares. Counted in Areas, so checkable against path patterns.
   - **Novelty:** familiar Area with good accuracy / some history or mixed accuracy / new Area, no history, or poor accuracy. Read from per-Area accuracy.
3. **Combination:** stakes = max(reversibility, blast radius). Depth from the stakes × novelty grid:

   | | Low stakes | Medium stakes | High stakes |
   |---|---|---|---|
   | New | light | full | full |
   | Some history | offer skip | light | full |
   | Familiar | offer skip | offer skip | light |

   Target selection ranks candidates by the same grid.
4. **Disclosure:** Claude scores silently and opens with depth and duration only (e.g. "Full version, about 5 min. Say 'light' or 'skip' to change that."). Scores and reasons appear at the reveal, beside the developer's answer, because stating them earlier hints at the design and gives away the Areas-touched prediction.
5. **Cold start:** a one-word familiarity estimate (new / some / familiar) folded into the Area-creation confirmation; no answer = new. It is a prior only; real per-Area accuracy replaces it after a few scored predictions. Git authorship was rejected: agent-written code is committed under the developer's name.
6. **Gate:** in `design-first`, if Claude finds no real alternatives, depth silently becomes "offer skip" regardless of stakes; the reason is given at the reveal (or on skip).
7. **Per skill:** `design-first` scores the task (after the gate) to set depth; `teach-back` ranks the feature's decisions and Areas to choose what to probe; `/revisit` ranks due items when more are due than fit in one session.

Left to the fading rules (Not yet specified): the accuracy cut-offs for "good" and "poor".

**Reopened during this ticket:** the Change-impact drill is folded into `design-first` and checked against the real diff, removing `change-impact` as a skill. Recorded as an amendment on [Milestone 1 lineup and build order](05-milestone-lineup.md).

Note: `/revisit` was later removed from Milestone 1, so its per-skill line (item 7) no longer applies.

Amendment (from [How design-first fits the planning flow](13-planning-flow-fit.md)): Milestone 1 stores no history, so novelty is Claude's in-session estimate (corrected by the developer's override), not read from per-Area accuracy; item 5's cold-start rule is moot. Blast radius counts Areas named in the session. Inside `design-first` the rubric also triages each grill question to decide whether Claude withholds its recommendation.

Amendment (from [Answer-first grilling as its own skill](14-answer-first-grilling.md)): the rubric triages each grill question in the answer-first skills (full → open question; light → options without recommendation; offer-skip → normal question) and selects Ripple questions (max two per session). The no-alternatives gate now applies per question: a question with no real alternatives is offer-skip.

Amendment (decided during [grill-me-answer-first flow](15-answer-first-flow.md), 2026-10-21): the grid and separate rubric file are replaced by a short rule inside the shared grill procedure, to cut tokens and keep calls consistent: "Hold back when a decision is costly to undo or touches several Areas. Ask it open when it is also new to this developer or is a one-way door; otherwise pick one. Everything else, and any question without real alternatives, is normal. Judge from what you already know; never read code just to triage." No score reveal. `teach-back` gets its own one-line rule when designed.
