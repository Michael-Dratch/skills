# Research: current Claude Code plugin and skill format

Ticket: [01-plugin-skill-format](../issues/01-plugin-skill-format.md). Researched 2026-10-20 against code.claude.com docs (fetched as raw `.md`) and `anthropics/claude-plugins-official`. Version numbers quoted are the docs' own.

Sources (abbreviated below as [S1]..[S9]):
- [S1] Skills: https://code.claude.com/docs/en/skills
- [S2] Plugin manifest reference: https://code.claude.com/docs/en/plugins/manifest-reference
- [S3] Add components to a plugin: https://code.claude.com/docs/en/plugins/components
- [S4] Create a marketplace: https://code.claude.com/docs/en/plugins/create-marketplace
- [S5] Marketplace reference: https://code.claude.com/docs/en/plugins/marketplace-reference
- [S6] Plugin loading reference: https://code.claude.com/docs/en/plugins/loading
- [S7] Subagents: https://code.claude.com/docs/en/sub-agents
- [S8] Hooks reference: https://code.claude.com/docs/en/hooks
- [S9] Test plugins with evals: https://code.claude.com/docs/en/plugin-evals
- [S10] Official marketplace repo: https://github.com/anthropics/claude-plugins-official (e.g. `plugins/skill-creator/`)

## 1. SKILL.md frontmatter [S1 "Frontmatter reference"]

All fields optional; `description` recommended. Unknown field names are silently ignored. Frontmatter is read only if `---` is line 1; unparseable YAML loads the skill with no fields set.

| Field | Notes |
|---|---|
| `name` | Command name; defaults to directory name. In a plugin it is the last segment: `/<plugin>:<name>`. Bare `/<name>` also works if not taken. |
| `description` | Used for model triggering. `description` + `when_to_use` truncated at 1,536 chars in the listing. |
| `when_to_use` | Appended to description. |
| `argument-hint`, `arguments` | Autocomplete hint; named positional args for `$name` substitution. |
| `disable-model-invocation` | `true` = slash-command only (see §2). |
| `user-invocable` | `false` = model-only, hidden from `/` menu. |
| `allowed-tools` / `disallowed-tools` | Pre-approve / remove tools for the invoking turn only (grant clears at next user message). |
| `model`, `effort` | Per-turn override; with `context: fork` sets the subagent's model. |
| `context: fork`, `agent`, `background` | Run the skill as an isolated subagent (see §5). `background` default `true` (v2.1.218+). |
| `hooks` | Registered on invocation, persist for rest of session (`once: true` to remove after first run). |
| `paths` | Globs; auto-load only when working with matching files. |
| `shell`, `metadata`, `license`, `compatibility` | Misc. `metadata` is free-form, ignored by Claude Code. |

Portability: claude.ai uploads / Skills API / `package_skill.py` accept only `name, description, license, compatibility, metadata, allowed-tools` and hard-error on others (e.g. `disable-model-invocation`). Plugin skills in Claude Code accept everything. [S1 "Using skill frontmatter outside Claude Code"]

Substitutions in the body: `$ARGUMENTS`, `$N`, `$name`, `${CLAUDE_SESSION_ID}`, `${CLAUDE_EFFORT}`, `${CLAUDE_SKILL_DIR}`, `${CLAUDE_PROJECT_DIR}`, and (plugin skills only) `${CLAUDE_PLUGIN_ROOT}`, `${CLAUDE_PLUGIN_DATA}`. Also substituted inside `allowed-tools` Bash rules. [S1 "Available string substitutions"]

Dynamic context: `` !`command` `` and ```` ```! ```` blocks run at load and inject output. [S1 "Inject dynamic context"]

Lifecycle: rendered SKILL.md enters the conversation once and stays; not re-read. After compaction, the most recent invocation of each skill is re-attached (first 5,000 tokens each, 25,000 combined). Keep SKILL.md under 500 lines; push detail to sibling files referenced from it. [S1 "Skill content lifecycle", "Add supporting files"]

### Minimal example (as documented)

```yaml
---
name: checkpoint
description: End-of-session model-accuracy check. Use when the developer runs /checkpoint.
disable-model-invocation: true
argument-hint: "[area]"
allowed-tools: Bash(${CLAUDE_PLUGIN_ROOT}/scripts/import-graph.sh *)
---

Run `${CLAUDE_PLUGIN_ROOT}/scripts/import-graph.sh $ARGUMENTS` ...
For the probe rubric, read ${CLAUDE_PLUGIN_ROOT}/shared/probes.md.
```
(Pattern of matching the same variable in body and `allowed-tools` to run a bundled script without a prompt is from [S1]; it shows `${CLAUDE_SKILL_DIR}`, and states `${CLAUDE_PLUGIN_ROOT}` is substituted in the same two places.)

## 2. Making a skill slash-command-only [S1 "Control who invokes a skill", "Restrict Claude's skill access"]

- `disable-model-invocation: true`: user can run `/name`; Claude cannot invoke it on its own. **Its description is not loaded into context at all** (zero per-turn listing cost). If Claude tries, Claude Code blocks the call and tells it not to reproduce the steps another way.
- Also blocks: preloading it into subagents via `skills:` [S7], and session-scoped scheduled tasks firing it.
- Typing `/name` at the start of a message runs it; `/name` mid-sentence counts as permission for Claude to run it that message.
- `skillOverrides` setting (`on`/`name-only`/`user-invocable-only`/`off`) does **not** apply to plugin skills; plugins are managed via `/plugin`.
- Stacking: at the terminal prompt, `/a /b args` loads up to 6 inline skills; expansion stops at a forked (`context: fork`) skill. `-p`/SDK loads only the first.

## 3. Plugin manifest and marketplace layout

Plugin layout [S2 "Standard layout"]: manifest at `.claude-plugin/plugin.json` (optional; only `name` required); everything else at plugin root: `skills/<name>/SKILL.md`, `agents/*.md`, `hooks/hooks.json`, `.mcp.json`, `bin/`, `settings.json`, plus any free folders (e.g. `scripts/`, `shared/`). A `CLAUDE.md` at plugin root is **not** loaded (validate warns); put instructions in a skill. `commands/` is legacy; prefer `skills/`.

Names: kebab-case; may not start with `claude-`/`anthropic-`, etc. Every component is namespaced `<plugin>:<name>`. [S2 "name"]

Minimal `plugin.json` (fields per [S2] Fields table):
```json
{
  "name": "learning-skills",
  "version": "0.1.0",
  "description": "Learning-focused skills for developers working with Claude Code",
  "author": { "name": "Michael Dratch" },
  "license": "MIT"
}
```
`version` set = users pinned until you bump it. Other useful keys: `skills` (extra dirs, adds to default scan), `agents` (replaces scan), `hooks`, `dependencies`, `userConfig`, `experimental.evals`.

Marketplace [S4, S5]: a directory with `.claude-plugin/marketplace.json` requiring `name`, `owner`, `plugins[]`; each entry `name` + `source`. Relative sources must start with `./`; `"."` alone means the marketplace root itself, so **one repo can be both the marketplace and the single plugin**. Keep entry `name` == manifest `name`.
```json
{
  "name": "my-skills",
  "owner": { "name": "Michael Dratch" },
  "plugins": [
    { "name": "learning-skills", "source": ".", "description": "Learning-focused skills" }
  ]
}
```
Install: `claude plugin marketplace add <owner/repo or ./path>` then `claude plugin install learning-skills@my-skills`. Validate with `claude plugin validate <dir>` (`--strict` for CI). Local dev without a marketplace: `--plugin-dir` [S2, S6].

## 4. Bundled scripts and shared files across skills

- `${CLAUDE_PLUGIN_ROOT}` = installed plugin dir; usable in any plugin skill/agent/command Markdown body and in hook/MCP/LSP commands. Docs explicitly: "reference scripts or files bundled anywhere in the plugin, including resources shared between the plugin's skills." [S1, S2 "Environment variables"]
- `${CLAUDE_SKILL_DIR}` = the skill's own subdirectory (not plugin root). [S1]
- `${CLAUDE_PLUGIN_DATA}` = `~/.claude/plugins/data/<id>/`, survives updates; use for state/caches/deps. `${CLAUDE_PLUGIN_ROOT}` changes per version: **don't write state there**. Data dir deleted on uninstall unless `--keep-data`. [S2, S3]
- Gotcha: these variables are **not** in the Bash tool's environment; they are substituted textually in the Markdown body, so write them in the skill text. [S2 "Where each variable resolves"]
- Marketplace plugins are copied to `cache/<marketplace>/<plugin>/<version>/`; files outside the plugin dir (e.g. `../shared`) are not copied, and component paths escaping the root are rejected. Keep shared material inside the plugin. [S6]
- `bin/` executables go on the Bash `PATH` while enabled, but a plugin with top-level `bin/` won't install on claude.ai/Cowork. [S2]
- Project-side state (e.g. a learner profile in the repo) can be addressed via `${CLAUDE_PROJECT_DIR}`. [S1]

## 5. Skill-to-skill invocation and subagents

Skill invoking skill: Claude invokes skills through the `Skill` tool, so instructions in one skill can tell Claude to invoke another **model-invocable** skill. A target with `disable-model-invocation: true` is blocked from that path [S1]. Implication: shared logic needed by several slash-only skills should live in shared files read via `${CLAUDE_PLUGIN_ROOT}`, or in a model-invocable (optionally `user-invocable: false`) helper skill. (The docs don't describe skill-to-skill calls as a feature; this is inferred from the Skill-tool and blocking rules.)

Subagent context isolation [S7 "What loads at startup"]:
- Non-fork subagent starts with a fresh context: its own system prompt, Claude's **delegation message**, CLAUDE.md hierarchy, git status, preloaded `skills:`. It does **not** see conversation history, invoked skills, or files already read. `omitClaudeMd: true` drops CLAUDE.md; built-in Explore/Plan skip CLAUDE.md and git status.
- A **fork** (Claude-initiated by default, or `/subtask`) inherits the full conversation; no input isolation.
- `context: fork` in a skill is **not** a conversation fork despite the name: it spawns a fresh subagent (type from `agent`, default `general-purpose`) whose prompt is the SKILL.md content; no conversation history. Runs in background by default; `background: false` waits. Background forks get a narrower tool set and their edits bypass checkpoints. [S1 "Run skills in a subagent"]
- Plugins can ship agents in `agents/` (`<plugin>:<name>`). Supported plugin-agent frontmatter: `name, description, model, effort, maxTurns, autoCompactWindow, tools, disallowedTools, skills, memory, background, omitClaudeMd, isolation (worktree only), color`. Ignored in plugins: `permissionMode, hooks, mcpServers, initialPrompt`. [S3 "Agents"]
- Nesting: subagents can spawn subagents up to 3 layers (`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH`); 20 concurrent default. [S7]
- Relevance to `design-first` "design without reading the sketch": a non-fork subagent sees only what the delegation message and its readable files give it, so it can design blind if the sketch isn't in the delegation text (e.g. sketch saved to a file the agent is told not to read, or collected after the subagent returns). A fork or the main thread would already have seen it.

## 6. Hooks (for later) [S8, S3]

Plugin hooks: `hooks/hooks.json` (or manifest `hooks`). Events include `SessionStart`, `SessionEnd`, `UserPromptSubmit`, `UserPromptExpansion` (slash-command expansion), `PreToolUse`, `PostToolUse`, `Stop`, `SubagentStart/Stop`, `PreCompact/PostCompact`, `TaskCompleted`, `FileChanged`, among others. Handler types: command, http, MCP tool, prompt, agent. Hooks receive `CLAUDE_PLUGIN_ROOT`/`CLAUDE_PLUGIN_DATA`/`CLAUDE_PROJECT_DIR` in env. Skill-frontmatter hooks persist for the session after invocation; subagent-frontmatter hooks only while it runs (but are ignored in plugin agents). A `Stop`/`SessionEnd` hook is the documented mechanism for "suggest /checkpoint at session end".

## 7. Skill eval tooling

`claude plugin eval` [S9] (GA; v2.1.269+; announced week 37, Sept 2026):
- Suite in `evals/` (or `experimental.evals` / `--eval-dir`). One dir per case: `prompt.md` (frontmatter: `runs`, `max_turns`, `timeout_seconds`, `allowed_tools`, `model`, `tags`, `append_system_prompt`, `env`; body = prompt) and/or `case.yaml` (`context.scaffold_script`, `context.history_file` to resume a `.jsonl` transcript, `context.add_dirs`), plus `graders/*.md`.
- Grader types: `regex`, `tool_used` (e.g. `tool: Skill` + `input_match`), `tool_order`, `file_exists`, `llm` (rubric, 2-of-3 judge votes), `baseline` (compare to reference transcript). Targets: `last_message`, `trace`, `files`, a file's contents, `mock_calls`.
- Each case runs 3x with plugin and 3x without (no-plugin baseline); reports WITH, W/OUT, Δ. "Skill was invoked" graders are excluded from Δ scoring automatically. `--ablation none` for single arm; `--threshold` gates CI (non-zero exit). `claude plugin eval init` has Claude draft the suite interactively.
- Isolation: each run is a `claude -p` child in a temp home/workdir with only the plugin loaded; no user CLAUDE.md, settings, other plugins, or memory. Runs never prompt; only read-only tools from `allowed_tools` (incl. `Skill`, `AskUserQuestion`, `Agent`) unless `--allow-tools`; Bash runs sandboxed. Artifact tool off. Costs real model usage.
- Implications for this project: runs are single-shot non-interactive, so multi-turn learning dialogs need `context.history_file` to seed prior turns (the case's prompt is the next user turn). For slash-only skills, a trigger-test case is meaningless; a case prompt would invoke the command directly (inferred from `-p` loading the leading skill [S1 "Stack several skills"]; not stated on the evals page). `/skill-doctor` reports per-skill context cost and usage [S1 "Find unused skills"].

skill-creator plugin [S1 "Run evals with skill-creator", S10]: `/plugin install skill-creator@claude-plugins-official`; in-conversation loop with its own `evals/evals.json` (incompatible with `claude plugin eval`): per-case subagent runs, `grading.json`, with/without-skill `benchmark.json`, blind A/B between versions, description-trigger tuning, HTML review viewer. Format spec: https://agentskills.io/skill-creation/evaluating-skills. Note both tools default to `evals/`; use `--eval-dir` if both are used.
