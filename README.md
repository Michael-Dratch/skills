# skills

A Claude Code marketplace (`my-skills`) holding one plugin, `learning-skills`: skills for agentic coding that keep you doing the design thinking, so you stay the holder of your system's Theory. Principles: [`docs/principles.md`](docs/principles.md).

## Install

Requires Claude Code 2.1.275 or later.

```sh
claude plugin marketplace add Michael-Dratch/skills
claude plugin install learning-skills@my-skills
```

or, inside a session: `/plugin install learning-skills --marketplace Michael-Dratch/skills`.

### Dependency

`learning-skills` needs Matt Pocock's skills, `mattpocock-skills@claude-plugins-official` (it loads his `grilling` skill). The plugin declares this in `plugin.json` `dependencies`, and the marketplace allows dependencies from `claude-plugins-official`, so installing `learning-skills` should install it too. If it does not, install it yourself:

```sh
claude plugin install mattpocock-skills@claude-plugins-official
```

Without it the plugin is disabled.

To try a local checkout without installing:

```sh
claude --plugin-dir <path to mattpocock-skills> --plugin-dir ./plugins/learning-skills
```

## Commands

All commands are developer-invoked only; Claude never runs them on its own.

- `/grill-me-answer-first <feature or plan>` (also `/learning-skills:grill-me-answer-first`): a normal `/grill-me` session, except on the questions that matter (marked 🧠) you answer first. Claude critiques your answer, then shows its recommendation, and you lock in your choice. Up to two Ripple questions check your predictions of what code depends on a decision. Ends with a Decisions block for `/to-spec`; say *save* to write it to `.scratch/<feature-slug>/decisions.md`. Say *just tell me* at any time.

## Dogfood protocol

- Use the commands on current projects, where the features are real.
- After each session, write a one-line note: helped / annoyed / skipped, plus why.
- Review the notes after about two weeks or ten sessions, whichever comes first, and tune the skills from them.

## Layout

- `.claude-plugin/marketplace.json`: the marketplace.
- `plugins/learning-skills/`: the plugin (`skills/`, and `shared/` for procedures several skills read).
- `docs/principles.md`: the rules every skill follows.
- `.scratch/`: specs and tickets (see `docs/agents/issue-tracker.md`).
