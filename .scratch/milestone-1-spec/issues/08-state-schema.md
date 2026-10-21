# State schema

Type: grilling
Status: closed (out of scope)
Blocked by: 01, 04, 07, 13

## Question

What is the persisted state: directory name (replacing the provisional `.design-model/`), committed vs. gitignored, file formats, the records (decision, prediction, learner profile per Area; no review queue, since `/revisit` is out of scope), and the staleness rule for predictions whose code has since changed? Per "What is an Area of the system", it must also store Area definitions (name + path patterns), the file paths each record is about (needed for splits), and per-Area scores.

## Closed: out of scope

[How design-first fits the planning flow](13-planning-flow-fit.md) decided Milestone 1 persists nothing, so there is no state to specify.
