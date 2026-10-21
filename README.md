# skills

A Claude Code marketplace (`my-skills`) holding one plugin, `learning-skills`: skills for agentic coding that keep you doing the design thinking, so you stay the holder of your system's Theory. Principles: [`docs/principles.md`](docs/principles.md).

## Install

```sh
claude plugin marketplace add Michael-Dratch/skills
claude plugin install learning-skills@my-skills
```

### Dependency

`learning-skills` needs Matt Pocock's skills, `mattpocock-skills@claude-plugins-official` (it loads his `grilling` and `domain-modeling` skills). The plugin declares this in `plugin.json` `dependencies`, and the marketplace allows dependencies from `claude-plugins-official`, so installing `learning-skills` should install it too. If it does not, install it yourself:

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
- `/grill-with-docs-answer-first <feature or plan>` (also `/learning-skills:grill-with-docs-answer-first`): the same session as `/grill-me-answer-first`, but like `/grill-with-docs` it also records ADRs and `GLOSSARY.md` terms (via Matt Pocock's `domain-modeling`) as decisions settle.
- `/teach-back [feature, files, or branch]` (also `/learning-skills:teach-back`): explain a finished feature to Claude playing a new teammate, who keeps asking *why* until each thread reaches a constraint, a tradeoff, or 🕳️ no theory. You rate your understanding (1–7) before and after; then each load-bearing claim is checked against the code, ADRs, spec and `decisions.md` (✅ ◐ ❌ ❔), with ⚠️ drift findings and your biggest gaps. Defaults to the current branch's diff. About 5 minutes; *skip* ends a thread, *stop* jumps to the reveal. Nothing is saved.

These skills are early and evolving; feedback is welcome.

## Layout

- `.claude-plugin/marketplace.json`: the marketplace.
- `plugins/learning-skills/`: the plugin (`skills/`, and `shared/` for procedures several skills read).
- `docs/principles.md`: the rules every skill follows.
- `.scratch/`: specs and tickets (see `docs/agents/issue-tracker.md`).
