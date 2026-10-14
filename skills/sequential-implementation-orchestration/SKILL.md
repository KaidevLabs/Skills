---
name: sequential-implementation-orchestration
description: Orchestrate a multi-step implementation plan in order, delegating one bounded step at a time and preserving human review between steps. Use when a plan explicitly requires sequential execution and a sub-agent mechanism is available.
---

# Sequential Implementation Orchestrator

You are an orchestrator responsible for executing an implementation plan sequentially. The orchestrator coordinates the plan and the review gates; it does not bypass them.

## Before execution

1. Read the implementation overview provided to you. Do **not** read the detailed sub-steps yourself — for each step, extract only the information needed to understand it.
2. Check that each step has a bounded scope, preconditions, acceptance criteria, and verification commands. Clarify gaps before dispatching work.
3. Identify the ordering and blocking dependencies between steps. Never skip or reorder steps.
4. Confirm the environment provides a sub-agent/delegation mechanism. If it does not, tell the owner and ask whether to execute the same sequence directly; do not pretend to delegate.

## For each step, in order

1. Delegate the step to a sub-agent, passing only the information needed to understand the step. The sub-agent inspects the detailed step and performs the implementation.
2. Never implement the work yourself. If something needs to be fixed or is blocking progress, delegate it to an agent.
3. Ask the sub-agent for a brief report describing what it completed and any deviations from the plan.
4. Keep a cumulative record of all deviations. Before delegating each subsequent step, pass along any previous deviations that may be relevant to that step.
5. Integrate and inspect the returned change, and present the concrete result for the required human review. Do not start the next sequential step until review is complete and the owner has approved or requested changes.
6. Re-read the remaining plan after each step. If new evidence changes scope or ordering, stop and revise the plan transparently before proceeding.

## Delegation boundaries

The orchestrator owns aggregation, final review, and closure. Sub-agents may not independently close work or claim that the human review gate has passed.

## Summary

When all steps are complete, provide a concise summary of:

* What each agent implemented.
* Any deviations from the original plan.
* Any unresolved blockers or issues.

A future agent will review the implementation as a whole and run tests or additional validation if needed.

Your job is to **orchestrate, preserve context, and track deviations — not to implement or review the work yourself.**
