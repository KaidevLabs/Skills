---
name: sequential-implementation-orchestration
description: Orchestrate a multi-step implementation plan in order, delegating one bounded step at a time and preserving human review between steps. Use when a plan explicitly requires sequential execution and a sub-agent mechanism is available.
---

# Sequential implementation orchestration

The orchestrator coordinates the plan and review gates; it does not bypass them. Work from the authoritative plan, normally the Epic and Task issues when using Kaidev's tracker. See [GitHub tracker conventions](../../guidelines/tracker-github.md).

## Before execution

1. Read the complete plan, repository instructions, relevant decision records, and verification guidance.
2. Check that each step has a bounded scope, preconditions, acceptance criteria, and verification commands. Clarify gaps before dispatching work.
3. Identify ordering and native blocking dependencies. Confirm only the next eligible step is being started.
4. Confirm the environment provides a sub-agent or delegation mechanism. If not, tell the owner and ask whether to execute the same sequence directly; do not pretend to delegate.

## For each step

1. Dispatch exactly one implementation Task at a time unless the plan expressly identifies independent work that can safely run in parallel.
2. Give the executor the Task specification, parent decisions, allowed scope, explicit exclusions, relevant repository guidance, test seams, and exact verification instructions.
3. Ask it to return a concise summary of changes, files touched, commands and results, deviations, blockers, and decisions needing review. It must not select or begin a later Task.
4. Integrate and inspect the returned change. Verify scope boundaries, important behavior, and reported checks; run required verification in the owning context when practical.
5. Present the concrete result for the required human review. Do not start the next sequential step until review is complete and the owner has approved or requested changes.
6. After review, record the as-built result, commit or stage only as authorized by the owner, and update the Task/Epic status through the established workflow.
7. Re-read the remaining plan after each step. If new evidence changes scope or ordering, stop and revise the plan transparently before proceeding.

The orchestrator owns aggregation, final review, plan updates, and closure. Sub-agents may not independently close issues or claim that the human review gate has passed.
