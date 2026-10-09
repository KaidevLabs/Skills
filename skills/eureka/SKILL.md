---
name: eureka
description: Discover and settle requirements before implementation planning. Use when the goal, constraints, current behavior, or design decisions remain unclear.
---

# Eureka: discovery before planning

Turn an initial idea into an evidence-backed, reviewable specification. Do not jump to implementation or task breakdown while important decisions remain unresolved.

1. **Understand the outcome.** Restate the desired user or system outcome and why it matters. Separate stated goals from assumptions.
2. **Gather evidence.** Inspect relevant code, documentation, tests, issues, and behavior available in the repository. Cite paths, symbols, outputs, or links; distinguish facts from inference.
3. **Map current behavior.** Trace the affected flow, including callers, persistence, interfaces, integrations, and failure cases. Identify constraints and adjacent behavior that must remain stable.
4. **Resolve decisions with the owner.** Ask one focused question at a time, ordered by impact and dependency. Offer concrete options. Respectfully challenge choices that conflict with evidence or leave important behavior undefined; do not silently decide product behavior.
5. **Track uncertainty.** Maintain settled decisions, open questions, assumptions, and out-of-scope items. State which work depends on an open answer.
6. **Define completion.** Turn the agreement into observable, verifiable acceptance criteria, including relevant edge and failure cases.
7. **Hand off to planning.** When scope is settled, produce a concise specification ready for ordered implementation steps. For Kaidev's shared GitHub issue workflow, continue with the `plans` skill and [tracker guideline](../../guidelines/tracker-github.md).

## Output

Present: goal and impact; current behavior and evidence; settled decisions and rationale; proposed behavior and acceptance criteria; open questions and assumptions; out of scope and compatibility constraints; risks, dependencies, and likely verification seams. If an essential decision remains open, say what it blocks and ask the next single question. Do not call the specification settled prematurely.
