# How to enforce withhold-then-reveal

Type: research
Status: resolved
Blocked by: 

## Question

What mechanisms in Claude Code can make withhold-then-reveal reliable rather than relying on instruction-following? Candidates: the developer's sketch saved to a file Claude reads only after writing its own design; a subagent that designs with no access to the sketch or conversation; a hook that blocks reading the sketch until a condition holds. For each: does it work as described, what it costs in latency and friction, and how it fails.

## Answer

Make the order structural instead of relying on Claude choosing not to look. Once a sketch is in the main conversation, no hook or file gate can remove it, so the sketch must not exist anywhere Claude can see until Claude's design is committed. Recommended flow:

1. The developer states the task only.
2. A background, isolated subagent (plugin agent or `context: fork` skill) designs from the task alone. It writes `claude-design.md` and returns only the path. This keeps the sketch from Claude and keeps Claude's design (and hints) from the developer and from the main thread.
3. The developer writes the sketch while the subagent runs, so its latency is hidden.
4. The main thread reveals and compares.

Backstop: a plugin PreToolUse hook denies Read/Grep/Bash access to the sketch path until the design file exists. It is best-effort, since `grep -r` and scripts can bypass it. Fallback: commit-first in the main thread (Claude writes its design file before asking for the sketch). Residual risk: design choices already in the task statement; cover it with evals.

Details and citations: [research/03-enforce-withholding.md](../research/03-enforce-withholding.md)
