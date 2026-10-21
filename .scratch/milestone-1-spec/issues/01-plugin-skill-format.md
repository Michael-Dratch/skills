# Current Claude Code plugin and skill format

Type: research
Status: resolved
Blocked by: 

## Question

What are the current, documented formats and capabilities for Claude Code skills and plugins? Specifically: SKILL.md frontmatter fields (including how to make a skill slash-command-only / not model-invoked); plugin manifest and marketplace layout so the set installs as one plugin; how a skill references bundled scripts and shared files (e.g. plugin-root variables); whether one skill can invoke another; subagent capabilities and context isolation; hooks (for later); and `claude plugin eval` or other skill-eval tooling. Primary sources only.

## Answer

- Slash-only: `disable-model-invocation: true` (description then costs zero context; Claude is blocked from invoking it, including via another skill or subagent `skills:` preload). `user-invocable: false` is the inverse.
- Packaging: one repo can be both marketplace and plugin (`.claude-plugin/marketplace.json` with `"source": "."` plus `.claude-plugin/plugin.json`); skills in `skills/<name>/SKILL.md`, namespaced `/<plugin>:<name>`.
- Shared files: keep them inside the plugin and reference via `${CLAUDE_PLUGIN_ROOT}` in skill Markdown (substituted textually, not in Bash env); persistent state in `${CLAUDE_PLUGIN_DATA}` or the project via `${CLAUDE_PROJECT_DIR}`.
- Subagents: non-fork subagents (and `context: fork` skills) start with no conversation history, only the delegation message/skill text + CLAUDE.md; forks inherit everything. Plugins can ship `agents/`.
- Evals: `claude plugin eval` (GA) runs isolated `-p` sessions with vs. without the plugin, graders regex/tool_used/llm/etc., `history_file` for multi-turn seeding; skill-creator plugin offers an in-session alternative with an incompatible format.

Details and citations: [research/01-plugin-skill-format.md](../research/01-plugin-skill-format.md)
