# Enforcing withhold-then-reveal in Claude Code

Question: [issues/03-enforce-withholding.md](../issues/03-enforce-withholding.md). Context: planning_doc.md §5.1, §6 `design-first`, §11 Q3.
Sources: Claude Code docs, fetched 2026-10-20.

- [SA] https://code.claude.com/docs/en/sub-agents
- [SK] https://code.claude.com/docs/en/skills
- [HK] https://code.claude.com/docs/en/hooks
- [PM] https://code.claude.com/docs/en/permissions
- [PR] https://code.claude.com/docs/en/plugins-reference

## Two separate leaks

The skill has to block two leaks, and each mechanism handles a different one:

1. **Sketch → Claude** (Claude anchors on the developer's design): this ruins the comparison.
2. **Claude → developer** (Claude's design or hints reach the developer before they commit): this ruins the generation effect. §5.1 is about this one.

**The main constraint:** once the sketch is in the main conversation (typed in chat, @-mentioned, pasted), no file gate or hook can take it back out. A UserPromptSubmit hook can block or add context to a prompt, but the docs list no way to edit or redact it; `suppressOriginalPrompt` only changes the block message [HK]. An `@file` mention of a denied path is blocked by a `Read` deny rule [PM], but the docs say nothing about plain pasted text. So any reliable design has to control **when** the sketch enters a context Claude can see, not just whether Claude reads it.

## Candidates

### A. Ordered file reads (developer writes sketch to a file; skill tells Claude not to open it until its own design is written)
- **Works as documented?** Only through instruction-following. No platform mechanism enforces it.
- **Cost:** none in latency. Small friction: the developer writes to a file instead of chat.
- **Failure modes:**
  - Claude reads the file early (helpfulness, or a repo-wide `Grep` that returns the file's lines).
  - The developer types the sketch in chat anyway.
  - The main thread, holding its own design, hints while it asks for the sketch (leak 2).

### B. Commit-first ordering (Claude writes its design to a file *before* the sketch is requested)
- **Works as documented?** Yes, structurally. The sketch doesn't exist yet when Claude designs, so leak 1 can't happen. No platform feature is needed.
- **Cost:** none.
- **Failure modes:**
  - Leak 2 stays open: the main thread knows its design and can hint while it asks for the sketch.
  - The Write tool call is shown in the developer's terminal. Unverified whether the terminal shows the written content; check by dogfooding.
  - The task statement itself may already hold design choices ("add a cache in the repo layer").

### C. Isolated subagent designs (custom agent, or skill with `context: fork`)
- **Works as documented?** Yes.
  - "Each subagent starts with a fresh, isolated context window. It doesn't see your conversation history… Claude composes a delegation message" [SA].
  - `context: fork` on a skill: "The subagent doesn't see your conversation history", and despite the name it is *not* a conversation fork [SK]. A real fork (`/subtask`) "inherits the entire conversation", so it gives no isolation. Never use one here [SA].
  - The subagent's tool calls stay out of the main conversation; only its final result comes back [SA]. So if the subagent writes its design to a file and returns only the path, neither the developer nor the main thread sees the design. That closes leak 2 as well.
  - Tools can be restricted with `tools` / `disallowedTools` [SA].
- **Cost:**
  - One extra agent run that starts cold and has to re-read the relevant code. Expect roughly 30 seconds to a few minutes (estimate).
  - `context: fork` runs in the background by default; `background: false` makes the turn wait (v2.1.218+) [SK]. In background mode the latency is **hidden**: the developer writes the sketch while the subagent designs.
  - Plugin subagents ignore `hooks` and `permissionMode` [SA], but `tools` still applies.
- **Failure modes:**
  - **The delegation prompt leaks the sketch.** The main thread writes the prompt, so anything already in the main context, including a sketch, can get copied in. This is the risk the ticket raised. Isolation only helps if the sketch is *not yet* in the main context when the subagent is spawned. So C needs B's ordering.
  - The task statement holds design choices (same as B).
  - The subagent returns its design inline instead of just a path. The agent prompt has to say "return only the path". A PreToolUse hook can't filter results; check whether a SubagentStop hook can.

### D. Hook-gated read (PreToolUse denies access to the sketch path until Claude's design file exists)
- **Works as documented?** Yes, for the built-in tools.
  - PreToolUse gets `tool_input.file_path` for Read ("always absolute") and `path` for Grep/Glob. It can deny with exit 2 or `permissionDecision: "deny"` plus a reason [HK].
  - A conditional check (does `claude-design.md` exist?) is easy in a script.
  - A static `Read(...)` deny rule can't be conditional, so a hook is the right tool [PM].
- **Where it lives:** plugin `hooks/hooks.json` merges with user and project hooks while the plugin is enabled [HK][PR]. Skill frontmatter hooks stay registered for the rest of the session once the skill runs, or until their first success with `once: true` [HK].
- **Cost:** milliseconds per tool call. One script, scoped by path, so near-zero friction.
- **Failure modes:**
  - **Bash bypass.** Read deny rules cover recognized commands (`cat`, `head`, `tail`, `sed`, redirections) but not `grep -r pattern .` or scripts that open files themselves; only the sandbox gives OS-level blocking [PM]. A hook on `Bash` sees only the command text, and the docs call `if` filtering "best-effort" [HK].
  - **Directory Grep.** A `Grep` over a directory that contains the sketch returns the sketch's lines, and a hook can't tell from `path` alone. Mitigation: keep the sketch outside searched trees, e.g. `${CLAUDE_PLUGIN_DATA}` [PR] or the session scratchpad, which hooks receive as `scratchpad_dir` [HK].
  - **Doesn't help with chat.** It does nothing for a sketch typed into chat (see the main constraint above).
- Claude isn't adversarial, so a best-effort gate against *accidental* reads is enough. Treat D as a backstop, not the main mechanism.

### E. Others considered
- **Static `Read` deny rule** [PM]: it can't be lifted mid-session without editing settings. Not suitable.
- **UserPromptSubmit hook** [HK]: it can detect a sketch typed early and block it with a message ("hold that, write it after Claude's design is locked"). Blocking is the escape hatch for leak 1 via chat, but it is intrusive and needs a heuristic or a state file. Optional.
- **`disable-model-invocation`** [SK]: only controls who can invoke the skill. Not relevant.

## Recommendation

Make the order structural, so that withholding never depends on Claude choosing not to look:

1. The developer names the task (goals and constraints only). The skill says in plain words: "don't describe your design yet."
2. The skill immediately starts an **isolated design subagent** (plugin agent, or `context: fork` running in the background) whose prompt is built from the task statement only. The subagent writes `.design-model/<id>/claude-design.md` and returns **only the path** (C).
3. Meanwhile the main thread asks for the developer's 2–5-line sketch, in chat or a file. The main thread hasn't seen Claude's design, so it can't hint (leak 2 closed). The subagent never had the sketch (leak 1 closed).
4. Once the subagent finishes and the developer has committed, the main thread reads both and presents the differences.
5. **Backstop (D):** a plugin `hooks/hooks.json` PreToolUse on `Read|Grep|Glob|Bash` denies any input that names `developer-sketch*` while `claude-design.md` is missing. The denial reason tells Claude to write its design first.
6. **Fallback if subagent latency or friction is too high in dogfooding:** commit-first in the main thread (B). The main thread writes its design file before asking for the sketch. Leak 1 stays closed; leak 2 then depends on instructions plus evals.
7. **Escape hatch:** "just tell me" skips step 3. The reveal reads the design file directly, and the hook doesn't fire because the design file exists.

**Residual risk no mechanism removes:** design choices embedded in the developer's task statement, or a sketch volunteered before the skill starts. Cover both with evals (§10 step 5) rather than mechanism.

**Verify while dogfooding:**
- Whether the Write tool display shows file contents to the developer (affects B).
- Whether frontmatter hooks in plugin *skills* are honored. Plugin *agents* are documented as not supporting them [SA]; `hooks/hooks.json` is the safe home.
