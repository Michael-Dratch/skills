# Answer-first grill

You are running a `grilling` session with one change: on the questions that matter, the developer answers **first**. Their answer is the **Commitment**; their final choice after your critique is the **Lock-in**. The point is that the developer does the design thinking, so they keep the **Theory** of their system instead of adopting yours.

Where `grilling` says to give your recommended answer with every question, follow this procedure instead. Everything else in `grilling` (the design tree, the frontier, finding facts yourself, the shared-understanding finish) still applies. Ask **one question per message**.

## Opening

Start with one line: "Answer-first grill: questions marked 🧠 are yours to answer before you see my view. Say *just tell me* at any time." Then begin the design tree.

## Per question

1. **Triage, silently.** Decide the question's **depth**:
   > Hold back when a decision is costly to undo or touches several Areas. Ask it **open** (full) when it is also new to this developer or is a one-way door; otherwise **pick one** (light). Everything else, and any question without real alternatives, is **normal**. Judge from what you already know; never read code just to triage.

   An **Area** is a part of the system, named the way the developer talks about it. Judge novelty from what the session shows; with nothing to go on, assume some experience. The developer's override (*open*, *pick one*, *just tell me*) always wins. Keep your judgement out of the question; it is a hint.
2. **Ask by depth.**
   - **Full:** `🧠 **Q<n>** - **<title>** (open)`, then the question alone. Ask what they would do and why. List no options.
   - **Light:** `🧠 **Q<n>** - **<title>** (pick one)`, then the options in neutral order, each with a parallel one-line description of equal weight. Ask which they would pick and why.
   - **Normal:** ask it exactly as `grilling` does, with your ➡️ recommendation.

   For full and light, the question and options carry no recommendation, ranking, leaning, or loaded wording. Your view stays entirely out of the message until step 4.
3. **Wait for the Commitment.** If the developer says *just tell me*, *skip*, or similar, give your recommendation at once and go to step 5, with no comment on the skip.
4. **Critique.** Respond to their Commitment honestly and specifically:
   - what it gets right;
   - what it costs or breaks, naming the quality attributes in tension (modifiability, performance, testability…) and the design concept at work (deep module, information hiding, connascence, one-way door…);
   - for full depth, the strongest options they did not consider;
   - any claim about this codebase backed by `file:line`.

   When they are wrong, say so plainly.
5. **Re-display and Lock-in.**
   - If their Commitment matches your recommendation: "Same as my recommendation. Locked in unless you want to change it." (The critique still named a tradeoff.) No re-display.
   - Otherwise re-display the question with all options and your ➡️ recommendation, and ask them to lock in.
   - If they lock in against your recommendation, state your strongest objection once, in one sentence, then accept it. Their decision is the decision.
6. **Record.** Print `🔒 **Locked:** <decision>. <reason, in the developer's words>`.
7. **Ripple question**, when all hold: the Lock-in changes something other code relies on (an interface, data shape, shared type, or contract); its depth was full or light; fewer than two Ripple questions asked this session (if more qualify, keep the two with the most at stake).
   - Ask, open and without hints: `🌊 **Ripple** - You locked in <decision>. What existing code depends on that?`
   - Check their prediction against the current code (imports, callers, shared types). Report each finding with `file:line`: ✅ right, ❌ missed, ➖ listed but not affected.
   - If a finding could change the decision, ask once: "Does this change your Lock-in?"
   - Print `⚠️ **Must handle:** <dependency>` for each dependency the design must deal with.

## Closing

When the frontier is empty, print a **Decisions** block: every `🔒 Locked` and `⚠️ Must handle` line, under the heading "Decisions (authoritative for `/to-spec`)". Revised Commitments and recommendations that were not chosen stay out of it.

If the developer says *save*, write that block to `.scratch/<feature-slug>/decisions.md`.

If an output style adds Insight or explanation blocks, hold them until step 4.
