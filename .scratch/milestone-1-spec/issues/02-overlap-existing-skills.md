# Overlap with existing skills and output styles

Type: research
Status: resolved
Blocked by: 

## Question

Where does this collection overlap with the installed Matt Pocock skills (especially `teach`, `grilling`, `domain-modeling` and its ADR format, `wayfinder`) and with Claude Code's built-in Learning and Explanatory output styles? For each overlap: what the existing thing does, what it doesn't do (commit-first, verified against code, spaced over time), and which conventions this collection should reuse rather than reinvent.

## Answer

Nothing installed combines commit-first, code-verified, and spaced-over-time; each existing piece has at most one. Position: complement, reuse formats.

- **design-first**: the Learning output style's `TODO(human)` anchors the developer (Claude builds the surroundings first) and its Guidance is a hint. `grilling` gives recommended answers, the opposite of withhold-then-reveal. Reuse design-it-twice's independent-subagent mechanics (the subagent also never sees output styles) and `rubber-duck`'s no-smuggled-hints and release-order rules.
- **checkpoint / teach-back**: `domain-modeling`'s "cross-reference with code" and `rubber-duck` are the nearest moves. `learn`'s "hold the line" contradicts the escape hatch, so diverge from it explicitly.
- **revisit**: no installed skill schedules retrieval. This is new ground. Reuse `teach`'s evidence-only and supersession rules.
- **Decision records**: match the ADR format (`0001-slug.md`, 1–3 sentences, lazy dir, Status/superseded) and add only the optional fields. Point to a real ADR when the three-criteria gate passes.
- **Triage**: share one-way/two-way door and blast-radius terms with `pr`. Avoid the name `/triage`, which collides with MP issue triage.
- **Output styles**: complement. Each SKILL.md suppresses Insight blocks until the reveal. Never use `force-for-plugin`.

Detail: [research/02-overlap-existing-skills.md](../research/02-overlap-existing-skills.md)
