# What is an Area of the system

Type: grilling
Status: resolved
Blocked by: 

## Question

What is an **Area**: the unit over which novelty, fading, and prediction accuracy are tracked? A directory, a module, a developer-named concept, a decision type, or a combination? How is it identified so the same Area is recognised across sessions as the code evolves?

## Answer

An **Area** is a part of *this* system the developer holds Theory about; the unit over which novelty, fading, and prediction accuracy are tracked.

1. **One axis.** Area = a part of the system. Decision type is a tag on records, never an Area (keeps data for a future cross-project Skill formation profile at no cost).
2. **Identity:** a developer-chosen name plus path patterns (e.g. **sync engine** → `src/sync/**`). The name ties it to the developer's Theory; the paths make it checkable against the code and stable across sessions.
3. **Creation:** lazy. The first time a skill persists something about code no Area covers, Claude proposes a name and paths; the developer accepts, renames, or adjusts in one line. No upfront setup; Claude never creates Areas silently.
4. **Multi-Area records:** a record lists every Area involved; accuracy is scored separately per Area.
5. **Flat, with a split operation.** No nesting. When misses cluster under one sub-path, Claude proposes a split; past records are reassigned using their saved file paths.
6. **Drift:** on load, Area patterns are checked against the code; when one matches nothing or much less, Claude proposes new paths via git rename detection and the developer confirms. Without git, it only flags Areas that match nothing.
7. **No overlap:** each file belongs to at most one Area. Overlap on creation is flagged; shared code gets its own Area (e.g. **data access**).

Consequences: **State schema** must store Area definitions, per-record file paths, and per-Area scores. **Triage rubric** reads novelty per Area.

## Amendment (from [How design-first fits the planning flow](13-planning-flow-fit.md))

Milestone 1 persists nothing, so the Area registry is moot: no stored definitions, path patterns, lazy creation, drift checks, splits, or overlap rule. An Area remains the vocabulary for a part of the system, named in the developer's terms during a session. Item 1 (decision type is not an Area) still stands.
