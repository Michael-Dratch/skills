# Project Brief: Learning-First Skills for Agentic Coding

> **For Claude Code:** This is the founding brief for this repository. Read it fully before proposing anything. It holds the thesis, the research base, the design principles every skill must follow, a catalog of candidate skills, and a first milestone. When it conflicts with your defaults (especially the default of being helpful by *telling*), this brief wins. Verify current Claude Code skill and plugin file formats against the official docs (https://docs.claude.com) before scaffolding; formats change, and this brief describes intent, not exact syntax.

---

## 1. Thesis

AI coding agents make it easy to ship systems you don't understand. That costs you three ways. You can't verify what the agent produced. You can't debug it when it breaks. And you stop building the expertise that makes you good at directing the agent in the first place.

This repo is a collection of **Claude Code skills that keep the developer cognitively engaged in the work that matters**: design decisions, tradeoffs, and their mental model of the system. The goal is not to slow development down everywhere. It is to spend the developer's attention where it buys the most understanding, at the moments when learning is cheapest.

The core idea comes from Peter Naur's *Programming as Theory Building* (1985). The real product of programming is a **theory** held in the developers' heads: why the system is shaped the way it is, how it responds to change. Code and docs can't fully hold that theory. An agent that writes all the code while the human only skims it produces software with no theory-holder. These skills exist to make sure the human stays one.

---

## 2. Evidence that this matters now

- **Shen & Tamkin (Anthropic), "How AI Impacts Skill Formation" (2026, arXiv:2601.20245).** In a randomized trial with developers learning an unfamiliar async Python library (Trio), the AI-assisted group scored about 17% lower on comprehension (roughly 50% vs. 67%), and the speed gains weren't statistically significant. **But how people used AI mattered more than whether they used it.** High-engagement patterns scored 65–86%, while low-engagement patterns (full delegation, iterating on AI output without understanding it) scored 24–39%. The high scorers asked conceptual questions, requested explanations, and coded parts independently. *This is close to a direct spec for this repo: make the high-engagement patterns the default path.* Caveat: the study measured comprehension right after the task, not long-term retention.
  - https://www.anthropic.com/research/AI-assistance-coding-skills
- **Bainbridge, "Ironies of Automation" (1983).** Automating most of a task leaves the human with the hardest part (handling failures) while removing the practice that would have prepared them for it. Agentic coding is a textbook case.
- **Lee et al., "The Impact of Generative AI on Critical Thinking" (CHI 2025).** Survey of knowledge workers: higher confidence in AI was associated with less critical thinking. Effort shifts from *producing* to *verifying and integrating*, and only some people make that shift.

---

## 3. Research foundations (and what each implies for skill design)

| Finding | Source | Implication for skills |
|---|---|---|
| **Self-explanation effect.** Explaining *why* in your own words builds better models than reading explanations. | Chi et al. 1989, 1994 | Skills ask; they don't lecture. The developer does the explaining. |
| **Generation effect.** Producing an answer before seeing one improves retention. | Slamecka & Graf 1978 | Developer commits to a view *before* Claude reveals its own. |
| **Testing effect / retrieval practice.** Retrieving from memory strengthens it more than re-reading. | Roediger & Karpicke 2006 | Periodic "recall your model" checks, not re-reading docs. |
| **Spacing effect.** Retrieval spread over time beats massed practice. | Cepeda et al. 2006 (meta-analysis) | Resurface past decisions/predictions on a schedule (Leitner boxes). |
| **Desirable difficulties.** Some friction during learning improves long-term retention even as it slows short-term performance. | Bjork 1994 | Friction is a feature, but only where it pays (see triage, §5). |
| **Productive failure.** Attempting a problem before instruction beats instruction first, even when the attempt fails. | Kapur 2008 | "Design first, then compare" beats "Claude designs, then quizzes you." |
| **Cognitive apprenticeship.** Make expert reasoning visible; scaffold, then fade. | Collins, Brown & Newman 1989 | Claude externalizes tradeoff reasoning; support recedes as competence grows. |
| **Expertise reversal effect.** Scaffolding that helps novices actively hurts experts. | Kalyuga et al. 2003 | Fading is required, not optional, and it should be *per area of the system*. |
| **Cognitive load theory / worked-example effect.** Novices learn best from worked examples; load must be managed. | Sweller 1988; Sweller & Cooper 1985 | New-to-you territory starts with worked examples, then faded examples, then independent work. |
| **Illusion of explanatory depth.** People overrate their understanding of mechanisms until they have to explain them step by step. | Rozenblit & Keil 2002 | "Rate, explain, re-rate" probes expose blurry areas cheaply. |
| **Prediction as a learning signal.** Predict, observe, explain; prediction error drives model revision. | White & Gunstone 1992 | Ask for predictions that can be checked against the code. |
| **Metacognitive calibration.** Knowing *what you don't know* is a separate, trainable skill. | Dunlosky & Metcalfe 2009 | Track confidence vs. accuracy over time; surface miscalibration. |
| **Learning by teaching / protégé effect.** Preparing to teach and explaining to a novice deepen understanding. | Chase et al. 2009; Fiorella & Mayer 2013 | "Naive teammate" mode: Claude asks "why" until you hit bedrock. |
| **Concept mapping.** Externalizing relationships between concepts reveals gaps. | Novak & Gowin 1984 | Draw the system from memory, then diff it against the real structure. |
| **Self-determination theory.** Engagement depends on autonomy, competence, and relatedness. | Deci & Ryan 1985, 2000 | Always offer an instant, guilt-free "skip / just tell me." Show progress. |

---

## 4. Design foundations (shared vocabulary)

Skills should use, and gradually teach, a precise design vocabulary. Experts reason in compressed chunks, and shared names are how those chunks form.

- **Parnas, "On the Criteria to Be Used in Decomposing Systems into Modules" (1972).** Decompose around *decisions likely to change*; hide each one behind an interface. This is the basis for change-impact drills.
- **Ousterhout, *A Philosophy of Software Design* (2018).** Deep vs. shallow modules, information hiding and leakage, complexity as dependencies plus obscurity, and his "red flags" (pass-through methods, temporal decomposition, etc.).
- **Connascence (Page-Jones 1992/1996).** A graded vocabulary of coupling (name, type, meaning, position, algorithm, execution, timing, identity) with strength, locality, and degree.
- **Brooks, *The Mythical Man-Month* (1975).** Conceptual integrity as the most important property of a system design.
- **Design rationale: Questions, Options, Criteria (MacLean et al. 1991).** Frame each decision as a question, the options considered, and the criteria that discriminate between them.
- **Architecture Decision Records (Nygard 2011).** Lightweight, append-only records of context, decision, and consequences.
- **Reversibility ("one-way vs. two-way doors").** How costly a decision is to undo determines how much deliberation it deserves.
- **Quality attributes and tradeoff analysis (Bass, Clements & Kazman; ATAM).** Decisions trade quality attributes (performance, modifiability, testability, etc.) against each other; naming the attributes makes the tradeoff explicit.

---

## 5. Principles every skill must follow

1. **Withhold, then reveal.** When the skill asks for the developer's view, Claude must not give its own answer, hints included, until the developer has committed to one. The SKILL.md must say so explicitly, because Claude's default helpfulness otherwise undermines the learning.
2. **Escape hatch, always.** "skip," "just tell me," or "not now" is honored immediately, without commentary or guilt. Log that it was skipped; don't argue.
3. **Triage before friction.** Deep engagement only when a decision scores high on:
   - **Reversibility**: how expensive is it to undo?
   - **Blast radius**: how much of the system depends on it?
   - **Novelty to this developer**: have they made this kind of call before? (Read from the learner profile; see §7.)

   Low-scoring decisions get a one-line note or nothing.
4. **Verify against reality.** Prefer prompts whose answers can be checked against the actual code: predictions, maps, traces. Self-reported understanding is unreliable (illusion of explanatory depth).
5. **Fade by area.** Scaffolding recedes per subsystem and per decision type as the developer's prediction accuracy rises. Novelty is local, not global.
6. **Short and bounded.** A learning interaction should usually take 2–5 minutes. Say up front how long it will take.
7. **Ask, don't lecture.** Explanations come *after* the developer's attempt, and focus on where their view and reality diverged.
8. **Thinking over documenting.** Persisted artifacts exist to support retrieval and track calibration, not to produce documentation. Keep entries terse.
9. **Respect the flow.** Never interrupt mid-implementation. Engage at natural boundaries: before significant work, at the end of a session, at the end of a feature.
10. **Honest feedback.** When the developer's model is wrong, say so plainly and specifically. Flattery destroys calibration.

---

## 6. Candidate skill catalog

Organized by when in the development process each one applies. These are candidates, not commitments; part of the work is deciding which to build first.

### Before building

**`design-first`: design first, then compare** *(highest priority)*
- **When:** before Claude implements something that scores high on triage.
- **How:** Developer writes a 2–5 line sketch (interfaces, where state lives, what calls what). Claude designs independently *without reading the sketch first* (the skill should have Claude write its design before opening the developer's), then presents the differences and what each one trades off. Disagreements are the learning surface.
- **Research:** generation effect, productive failure, avoiding anchoring.
- **Persisted:** the decision, both sketches (abbreviated), what was chosen and why, and a prediction to revisit later.

**`decision-frame`: structured tradeoff framing**
- **When:** at a real fork with multiple viable options.
- **How:** Frame the decision as Questions, Options, Criteria. The developer weights the criteria *before* Claude scores the options. Name the quality attributes in tension. Classify reversibility.
- **Research:** QOC rationale, ATAM, self-explanation.

**`counterfactual`: explore the road not taken**
- **When:** at architectural forks, used sparingly.
- **How:** "What would this look like event-driven / without the ORM / as two services?" Sketch the alternative at a high level; developer identifies what would get better and what would get worse.
- **Research:** contrasting cases (Schwartz & Bransford 1998); understanding a choice by seeing the design space around it.

### While building (at natural boundaries)

**`review-before-accept`: predict the diff**
- **When:** before reading a significant diff from Claude.
- **How:** Developer reads only the task and the file list, then predicts what changed and where the risk lies. Then they read the diff and compare. Claude highlights anything that surprised them.
- **Research:** prediction, generation, combating automation complacency.

**`hypothesis-debug`: hypothesis-first debugging**
- **When:** on a bug, before Claude investigates.
- **How:** Developer states a hypothesis and the evidence that would confirm or refute it. Claude investigates, then compares the developer's hypothesis to the actual cause.
- **Research:** scientific debugging (Zeller, *Why Programs Fail*), prediction error.

**`new-territory`: learning an unfamiliar library or pattern**
- **When:** the developer is working with something new to them (this is exactly the setting of the Shen & Tamkin study).
- **How:** Worked example, then a faded example (developer fills in key parts), then independent work with Claude available for conceptual questions only. The skill encourages the high-scoring interaction patterns from the study.
- **Research:** worked-example effect, faded worked examples (Renkl & Atkinson 2003), expertise reversal.

### After building (consolidation)

**`checkpoint`: model-accuracy check** *(highest priority)*
- **When:** end of a session or feature; developer-invoked, optionally suggested by a hook.
- **How:** Picks one of three probes, rotating:
  - **Change-impact drill.** Claude proposes a plausible future requirement; developer predicts which modules must change and why; Claude traces the real answer.
  - **Map from memory.** Developer sketches the module or dependency structure; a bundled script extracts the real import graph; Claude diffs them. Surprising edges often reveal design problems, not just knowledge gaps.
  - **Explanation-depth probe.** "Rate 1–7 your understanding of X. Walk me through it." Claude checks each step against the code, then the developer re-rates.
- **Research:** Parnas, concept mapping, illusion of explanatory depth, calibration.
- **Persisted:** prediction accuracy per area, which drives fading.

**`teach-back`: naive teammate**
- **When:** end of a feature, or before handing work off.
- **How:** Claude plays a smart new hire and keeps asking "why" until each explanation bottoms out in a constraint or a tradeoff. Explanations that bottom out in "that's how Claude wrote it" are flagged.
- **Research:** protégé effect, self-explanation.

**`design-critique`: vocabulary-driven review**
- **When:** periodically, on a module the developer chooses.
- **How:** Developer critiques the module first, using Ousterhout red flags and connascence. Claude adds what they missed, naming each concept. Builds the shared vocabulary over time.
- **Research:** cognitive apprenticeship (making expert perception visible), chunking.

### Across time

**`revisit`: spaced resurfacing**
- **When:** automatically at the start of any skill invocation, plus on demand.
- **How:** Reads due items from the shared state (Leitner boxes) and surfaces one or two: "Three weeks ago you predicted X would only touch storage. Still true? Check." Correct answers move an item to a longer interval; misses reset it.
- **Research:** spacing effect, retrieval practice.
- **Note:** this gets spacing without a background process. It piggybacks on invocations of other skills.

---

## 7. Shared infrastructure

Skills should share state and conventions rather than each inventing their own.

**State directory in the user's project:** `.design-model/` (name to be confirmed)
- `decisions/`: one terse file per decision: question, options, choice, rationale, reversibility, and a prediction to revisit.
- `review-queue.json`: Leitner-style items `{id, prompt, answer_ref, box, due, history[]}`.
- `learner-profile.json`: per-area and per-decision-type accuracy and confidence history; drives triage novelty and fading.
- Decide: is this committed to the repo (shared team theory) or gitignored (personal learning)? Possibly both, split.

**Shared triage rubric:** one documented rubric (reversibility × blast radius × novelty) that every skill references, so behavior is consistent.

**Bundled scripts:**
- Import/dependency graph extraction per language (start with one, likely TypeScript or Python).
- Review-queue scheduler (pick due items, update boxes).

**Hooks (optional, opt-in):** e.g., a session-end hook that *suggests* `/checkpoint`. Never forced.

---

## 8. Proposed repository structure

Verify against the current Claude Code plugin and skill documentation before scaffolding.

```
.
├── PROJECT_BRIEF.md            # this file
├── README.md                   # human-facing overview, install instructions
├── CLAUDE.md                   # short working rules for Claude Code in this repo (points here)
├── docs/
│   ├── research.md             # expanded annotated bibliography
│   ├── principles.md           # §5, canonical version
│   └── triage-rubric.md        # shared rubric
├── shared/
│   ├── state-schema.md         # .design-model/ formats
│   └── scripts/                # graph extraction, scheduler
├── skills/
│   ├── design-first/SKILL.md
│   ├── checkpoint/SKILL.md
│   ├── revisit/SKILL.md
│   └── ...
└── (plugin / marketplace manifest, per current docs, so the set installs as one plugin)
```

**SKILL.md conventions:**
- The `description` must say precisely when to use the skill, since that decides when Claude loads it. Most of these skills should be **developer-invoked** (slash command) rather than auto-triggered, to protect autonomy and flow.
- Each skill body includes: purpose; the research basis in one line; the triage check; the withhold-then-reveal rule stated explicitly; the escape-hatch rule; expected duration; what it persists; how it fades.
- Keep each SKILL.md short. Put long reference material in `docs/` and link to it.

---

## 9. How we'll know it works

- **Dogfood first.** Use the skills on real projects for several weeks before generalizing.
- **Leading signals:** prediction accuracy in `checkpoint` trending up per area; calibration gap (confidence minus accuracy) shrinking; skip rate per skill (a high skip rate means the skill is too frequent, too long, or not valuable).
- **Lagging signals:** the developer can debug and extend agent-written code without asking Claude to explain it; design discussions use the shared vocabulary naturally.
- **Skill evals:** for each skill, a small set of scenario prompts checking that Claude (a) withholds its answer until the developer commits, (b) honors skip immediately, (c) stays within its time budget, and (d) persists state correctly.

---

## 10. First milestone

1. Scaffold the repo structure and plugin manifest (verify the current format first).
2. Write `docs/principles.md` and `docs/triage-rubric.md` from §5.
3. Define the `.design-model/` state schema.
4. Build **`design-first`** and **`checkpoint`** (change-impact drill only, at first), plus the minimal `revisit` mechanism they both call.
5. Write evals for withhold-then-reveal and the escape hatch.
6. Dogfood on one real feature; capture what felt valuable vs. annoying; revise.

Defer: the import-graph map, the remaining catalog, hooks, multi-language support.

---

## 11. Open questions

- Personal learning vs. team theory: should `.design-model/` be committed, and does that change what gets written?
- How should novelty be estimated before there is any history: ask the developer, or infer from the codebase?
- Can Claude reliably "design without reading the developer's sketch first," or does the skill need a stricter sequence (e.g., the sketch saved to a file Claude opens only after writing its own)?
- What is the right default cadence for `revisit` so it feels helpful rather than nagging?
- How do these skills interact with Claude Code's built-in learning/explanatory output styles: complement, replace, or ignore?
- How do we measure long-term retention, given the key study only measured immediate comprehension?

---

## Appendix A: How this project started (context)

The idea began as a request for a single skill to be used incrementally while building a feature: to surface important design decisions and emerging patterns, engage deeply with tradeoffs, and build a stronger mental model of the system, grounded in learning research and design fundamentals.

A first proposal suggested four shapes:
1. A decision-point interrupt that detects new abstractions in diffs and asks Socratic questions.
2. A running externalized model the developer keeps updating.
3. Retrieval-practice revisits of past decisions.
4. A fading Socratic coach.

It recommended combining #1 and #3.

A review of that proposal found several problems:
- **The learning moment came too late.** Questions asked after Claude has written the code anchor the developer to Claude's design and turn reflection into rationalization. Commit-first (generation, productive failure) is stronger.
- **Detecting "real" decision points from diffs is noisy.** That led to the triage rubric instead.
- **Nothing checked whether the developer's model was actually correct.** That led to prediction-based, code-verified probes.
- **Journals tend to become documentation chores.** That led to terse, retrieval-oriented state.
- **It conflated the generation and testing effects.**

The review also added: Naur's theory-building view, the illusion of explanatory depth, productive failure, expertise reversal (which grounds fading), self-determination theory (the escape hatch), Anthropic's 2026 skill-formation study, and a design vocabulary (Parnas, Ousterhout, connascence, QOC, reversibility).

That analysis showed the space holds many distinct skills, each targeting a different learning mechanism or development phase. Hence this repo: a collection of research-backed, learning- and engagement-focused skills for agentic coding.

## Appendix B: Core references

- Bainbridge, L. (1983). Ironies of automation. *Automatica*.
- Bjork, R. A. (1994). Memory and metamemory considerations in the training of human beings.
- Brooks, F. (1975). *The Mythical Man-Month*.
- Cepeda, N. et al. (2006). Distributed practice in verbal recall tasks: A review and quantitative synthesis. *Psychological Bulletin*.
- Chase, C. et al. (2009). Teachable agents and the protégé effect. *J. Science Education and Technology*.
- Chi, M. T. H. et al. (1989). Self-explanations. *Cognitive Science*; Chi et al. (1994). Eliciting self-explanations improves understanding.
- Collins, A., Brown, J. S., & Newman, S. (1989). Cognitive apprenticeship.
- Deci, E. & Ryan, R. (1985, 2000). Self-determination theory.
- Dunlosky, J. & Metcalfe, J. (2009). *Metacognition*.
- Fiorella, L. & Mayer, R. (2013). The relative benefits of learning by teaching and teaching expectancy.
- Kalyuga, S. et al. (2003). The expertise reversal effect. *Educational Psychologist*.
- Kapur, M. (2008). Productive failure. *Cognition and Instruction*.
- Lee, H.-P. et al. (2025). The impact of generative AI on critical thinking. *CHI 2025*.
- MacLean, A. et al. (1991). Questions, options, and criteria: Elements of design space analysis.
- Naur, P. (1985). Programming as theory building.
- Novak, J. & Gowin, D. (1984). *Learning How to Learn*.
- Nygard, M. (2011). Documenting architecture decisions.
- Ousterhout, J. (2018). *A Philosophy of Software Design*.
- Page-Jones, M. (1992). Comparing techniques by means of encapsulation and connascence.
- Parnas, D. (1972). On the criteria to be used in decomposing systems into modules.
- Renkl, A. & Atkinson, R. (2003). Structuring the transition from example study to problem solving.
- Roediger, H. & Karpicke, J. (2006). Test-enhanced learning. *Psychological Science*.
- Rozenblit, L. & Keil, F. (2002). The misunderstood limits of folk science: An illusion of explanatory depth.
- Schwartz, D. & Bransford, J. (1998). A time for telling.
- Shen, J. H. & Tamkin, A. (2026). How AI impacts skill formation. arXiv:2601.20245.
- Slamecka, N. & Graf, P. (1978). The generation effect.
- Sweller, J. (1988). Cognitive load during problem solving.
- White, R. & Gunstone, R. (1992). *Probing Understanding*.
- Zeller, A. (2009). *Why Programs Fail*.