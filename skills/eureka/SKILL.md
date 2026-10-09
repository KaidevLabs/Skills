---
name: eureka
description: Discover and settle a change's requirements before implementation planning. Use when the goal, constraints, current behavior, or design decisions are still unclear and a reliable specification must be produced.
---

# Eureka: discovery before planning

Use this skill to turn an initial idea into an evidence-backed, reviewable specification. Do not jump to implementation or a task breakdown while important decisions are unresolved.

## Discovery protocol

1. **Understand the outcome.** Restate the desired user or system outcome and why it matters. Separate the stated goal from assumptions.
2. **Gather evidence.** Inspect relevant source, existing documentation, tests, issues, and runtime behavior available in the repository. Cite exact paths, symbols, outputs, or links. Distinguish observed facts from inference.
3. **Map the current behavior.** Trace the end-to-end path that the request affects, including callers, persistence, interfaces, integrations, and failure cases as relevant. Identify constraints and adjacent behavior that must remain stable.
4. **Resolve decisions with the owner.** Ask one focused question at a time, ordered by impact and dependency. Offer concrete options when useful. Challenge a choice respectfully when evidence suggests it conflicts with the goal, creates avoidable complexity, or leaves an important failure mode undefined. Do not silently decide product behavior for the owner.
5. **Track uncertainty.** Keep a short list of settled decisions, open questions, assumptions, and out-of-scope items. Remove a question only when the owner or evidence resolves it. Explain which downstream work depends on an open answer.
6. **Define completion.** Turn the agreed behavior into observable, verifiable acceptance criteria, including meaningful edge and failure cases. Avoid vague criteria such as "works correctly".
7. **Hand off to planning.** Once scope is sufficiently settled, produce a concise specification that can be divided into ordered, bounded implementation steps. For Kaidev's shared issue workflow, use the `plans` skill and [GitHub tracker guideline](../../guidelines/tracker-github.md).

## Output

Present the result in this order:

1. Goal and user impact.
2. Current behavior and evidence.
3. Settled decisions with rationale.
4. Proposed behavior and acceptance criteria.
5. Open questions and assumptions, if any.
6. Out of scope and compatibility constraints.
7. Risks, dependencies, and likely verification seams.

If essential decisions remain open, say what is blocked and ask the next single question. Do not present the specification as settled or create implementation Tasks prematurely.
