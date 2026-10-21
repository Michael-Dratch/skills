---
name: grill-with-docs-answer-first
description: Grill me about a plan, answering the significant design questions myself first, while recording ADRs and glossary terms.
disable-model-invocation: true
argument-hint: "[feature or plan]"
---

1. Call the Skill tool twice, for "grilling" and "domain-modeling". If either is not available, tell the developer this skill needs Matt Pocock's skills (`mattpocock-skills`) installed, and stop.
2. Read `${CLAUDE_PLUGIN_ROOT}/shared/answer-first-grill.md`. Where it differs from `grilling`, it wins.
3. Run the answer-first grill on: $ARGUMENTS
