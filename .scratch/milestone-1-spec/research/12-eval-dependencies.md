# 12. Evals for skills that depend on another plugin's skills

Question: our prototype skills (`plugins/learning-skills`, formerly the prototype `learning-skills-proto`) call the Skill tool for `grilling` and `domain-modeling` from `mattpocock-skills@claude-plugins-official` (1.3.1). Can `claude plugin eval` make those available?

Checked 2026-10-21 against Claude Code 2.1.303 (`claude --version`).

Sources:
- [E] Test plugins with evals: https://code.claude.com/docs/en/plugin-evals
- [M] Plugin manifest reference: https://code.claude.com/docs/en/plugins/manifest-reference
- [D] Plugin dependencies: https://code.claude.com/docs/en/plugins/dependencies
- [H] Run Claude Code programmatically: https://code.claude.com/docs/en/headless
- [S] Skills: https://code.claude.com/docs/en/skills
- [CLI] `claude plugin eval --help`, `claude plugin eval init --help`, `claude plugin prune --help` (run locally)

## 1. Can an eval case load another plugin or extra skills?

Documented answer: **no supported way except shipping them in the plugin under test.**

- Isolation, verbatim [E "How runs are isolated"]: "Each run gets a temporary home directory, working directory, and Claude Code configuration, and the agent under test runs there as a `claude -p` child process with only your plugin loaded." ... "Your user settings, hooks, `CLAUDE.md` files, MCP servers, other installed plugins, memory, and skills are absent. Project-scoped configuration isn't read anywhere either: no `.claude/` directory, `CLAUDE.md`, or `.mcp.json` loads from above the workspace or inside it, even one a `scaffold_script` wrote, and `add_dirs` directories grant read access only. ... **Ship any skills, agents, hooks, or MCP servers a case depends on in the plugin under test, since a `scaffold_script` can supply only files and git state.**"
- So each candidate mechanism is closed off:
  - `scaffold_script` writing `.claude/skills/`: not loaded ("even one a `scaffold_script` wrote"; "Use the script for files and git state only, since project configuration it writes isn't loaded" [E "Seed the workspace"]). It also runs only with `--scaffold`.
  - `context.add_dirs`: "grant read access only" [E]. (Contrast: plain `claude --bare --add-dir` *does* load skills from that dir's `.claude/skills/` [H "bare mode"], but eval explicitly overrides this.)
  - `--plugin-dir`-style flag on `claude plugin eval`: none exists in `--help` [CLI]. Target is a single "path, a plugin name, or a `plugin@marketplace` id".
  - plugin.json `dependencies`: the eval page never mentions dependencies; the only statement is "only your plugin loaded". Undocumented, so assume not resolved.
- One partial lead, **undocumented for this use**: the case field `plugins` [E "prompt.md frontmatter"]: "`plugins` | The nearest enclosing plugin | Plugin directories under test, relative to the case directory. Set `plugins: ["../.."]` when auto-detection doesn't find your plugin". It is an array of "plugin directories under test", so listing a second (vendored) plugin dir, e.g. `plugins: ["../..", "../../vendor/mattpocock-skills"]`, may load both. The docs only show the single-plugin use; whether multiple entries load together, whether a path outside the plugin root is accepted, and how the no-plugin baseline arm treats the second plugin are all unstated. Verify with one cheap run (`--runs 1 --ablation none --max-cost-usd`) before relying on it.

## 2. Can plugin.json declare a dependency on another plugin?

**Yes.** [M "Fields"]: "`dependencies` | Array of strings or objects | Plugins that must be enabled for this one to work". [M "dependencies"]: "Each entry is `"name"`, `"name@marketplace"`, or `{ "name": "...", "marketplace": "...", "version": "..." }`. Bare names resolve against this plugin's own marketplace."

Details from [D]:
- `version` is a semver range resolved against `<plugin-name>--v<version>` git tags on the dependency's repo.
- Cross-marketplace (our case: our marketplace -> `claude-plugins-official`): "By default, Claude Code doesn't install a dependency from a different marketplace than the declaring plugin's own, unless the user already has that dependency installed and enabled at the same scope." Fix: `allowCrossMarketplaceDependenciesOn: ["claude-plugins-official"]` in the root `marketplace.json`. Without it and with the dep declared in `plugin.json`, "the install completes without the dependency and your plugin then fails to load."
- Local dev [D "Test a plugin and its dependency locally"]: `claude --plugin-dir ./my-dependency --plugin-dir ./my-plugin` — "The local copy of the dependency satisfies your plugin's dependency entry". Without the dependency's flag "the error reports the dependency as not installed" and the dependent plugin is disabled.
- Implication for evals (inference): if we declare the dependency and eval loads "only your plugin", the plugin may fail to load at all in eval runs (an unsatisfied dependency disables the dependent plugin [D]). The eval page doesn't say eval exempts this. Test before adding `dependencies`.
- `claude plugin prune` "Remove auto-installed dependencies that are no longer needed" [CLI] confirms install-time auto-install.
- Skill names: dependency skills are namespaced like any plugin skill, `mattpocock-skills:grilling` [S "plugin namespacing"].

## 3. Multi-turn via `context.history_file` for slash-only skills

- [E "case.yaml fields"]: "`context.history_file` | A `.jsonl` transcript in the case directory to resume. The case's prompt becomes the next user turn". With a path target such a case runs single-arm by default ("on the assumption that the recorded conversation already reflects the plugin"); `--ablation with-without` forces both arms [E "Score against the no-plugin baseline"].
- The format is a Claude Code session transcript (`claude -p --resume` accepts "the absolute path to a session's `.jsonl` transcript file" [H "Continue conversations"]). The eval page doesn't specify what the transcript may contain.
- Skill content persists: "When you or Claude invoke a skill, the rendered `SKILL.md` content enters the conversation as a single message and stays there across later turns." [S "Skill content lifecycle"]. So a transcript recorded from a real session where the user typed `/learning-skills-proto:grill-me-answer-first ...` already holds the expanded skill and any `grilling` Skill-tool results, and resuming it should carry them. **Inference, not stated on the eval page.** Caveats: the resumed turn still can't call `grilling` again (not loaded), and the `${CLAUDE_PLUGIN_ROOT}` paths inside a recorded transcript point at the recording machine's paths.
- Alternative for turn 1 (no history): put the slash command at the start of `prompt.md`'s body. In `-p` mode "User-invoked skills and custom commands work. Include `/skill-name` in the prompt string and Claude Code expands it before running." [H] and "Through the Agent SDK or `claude -p`, Claude Code loads only the first skill, with the rest of the line as its arguments." [S]. `disable-model-invocation: true` blocks only Claude's own invocation, not the user's `/name` [S table: "You can invoke: Yes"]. The eval page doesn't explicitly confirm the prompt body goes through slash expansion (inferred from runs being `claude -p` children [E]).
- Grading note: a `/name`-started skill is not a Skill tool call, so a `tool_used: Skill` grader for our own skill would not see it (inference); use it only for the inner `grilling` call.

## 4. Grading "the reply does not contain a recommendation"

Two documented options [E "Grader types"]:

- `llm` grader, file body is the rubric; `focus` defaults to `last_message`; passes on 2 of 3 judge votes; judge defaults to haiku (`--judge-model` overrides [CLI]). Template from [E "Write a case manually"]:

  ```markdown
  ---
  type: llm
  ---

  PASS if <what a correct response contains>.
  FAIL if <what a wrong or missing response looks like>.
  ```

  e.g. `PASS if the reply asks the design question(s) and gives no recommended answer, preferred option, or "I'd suggest". FAIL if it states or hints at which option it recommends.` Optional keys: `weight`, `arm`, `focus` (`last_message` | `trace` | `files` | `{ source: file, path: ... }` | `mock_calls`; judge sees first 12 + last 12 messages of a `trace`).
- `regex` grader with absence: "Set `match: not_contains` to require absence" (also `match: "count:N"`, `flags: i`; inline `(?i)` unsupported). Useful as a cheap backstop for phrases like `recommend|I'd suggest`.
- Tool absence: `tool_used` with `min: 0` and `max: 0`; add `arm: both` for "must not invoke" checks so they're scored in both arms [E].

## Recommendation

The documented path [E] is: make the plugin under test self-sufficient. Concretely, for eval purposes:
1. Vendor `grilling` and `domain-modeling` (MIT) into an eval-only copy of the plugin, or a sibling plugin dir, and point the case at it. First try the `plugins: ["../..", "<vendored dir>"]` case field with one budget-capped run; if that doesn't load both, build a throwaway "eval bundle" plugin dir that contains our skills plus the two vendored skills and eval that.
2. Make our skills' `Skill` call name-agnostic (they already say "grilling"; a namespaced call such as `mattpocock-skills:grilling` would not match a vendored copy).
3. Don't add `dependencies` to plugin.json until a test run shows eval doesn't disable the plugin over it; if added later for real installs, set `allowCrossMarketplaceDependenciesOn: ["claude-plugins-official"]` in our marketplace.
4. Start slash-only cases with `/learning-skills-proto:<skill> ...` in `prompt.md`; use `context.history_file` for later turns; grade withholding with an `llm` rubric plus a `regex` `not_contains` backstop.
