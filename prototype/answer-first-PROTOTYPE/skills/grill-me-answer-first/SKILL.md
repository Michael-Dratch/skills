---
name: grill-me-answer-first
description: Grill me about a plan, answering the significant design questions myself before seeing Claude's recommendation.
disable-model-invocation: true
argument-hint: "[feature or plan]"
---

1. Call the Skill tool with "grilling". If it is not available, tell the developer this skill needs Matt Pocock's skills (`mattpocock-skills`) installed, and stop.
2. Read `${CLAUDE_PLUGIN_ROOT}/shared/answer-first-grill.md`. Where it differs from `grilling`, it wins.
3. Run the answer-first grill on: $ARGUMENTS
