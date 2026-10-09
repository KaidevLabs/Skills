---
name: plans
description: Turn settled scope into an ordered GitHub Epic and Task plan, then guide execution and closure using Kaidev's shared GitHub Projects conventions. Use after discovery when work needs multiple tracked steps.
---

# Plans: issue-backed implementation lifecycle

Use this skill after the goal and important behavior are sufficiently settled. The shared Kaidev workflow is defined in [GitHub tracker conventions](../../guidelines/tracker-github.md); follow it for project discovery, Epic and Task structure, dependencies, board state, and closure.

## Plan

1. Confirm the target repository and uniquely matching organization project at runtime. If the project cannot be identified unambiguously, pause and ask the owner.
2. Read the current issue, project, repository instructions, and relevant code. Reuse existing issues when appropriate; do not create duplicate Epics or Tasks.
3. Ensure discovery has settled the goal, current state, decisions, constraints, acceptance criteria, and out-of-scope behavior. If a material decision is unresolved, return to the eureka skill rather than hiding it in an implementation Task.
4. Write an Epic with the shared tracker template. Divide the work into ordered Tasks with clear behavior boundaries, explicit exclusions, exact verification commands derived from this repository's documentation, and test seams where behavior changes.
5. Add native Task sub-issue and blocked-by relationships. Ensure the graph and the Epic execution order agree. Add the Epic and Tasks to the matching GitHub Project and set statuses explicitly.
6. Review the complete plan for gaps: every acceptance criterion has an owner and verification path; all prerequisites precede dependent work; independent parallelism is explicitly marked; there are no tasks that combine unrelated changes.

## Execute

1. Start only an accepted, unblocked Task. Resolve its instructions and verify its preconditions in the repository before editing.
2. Execute one Task as a vertical, reviewable slice. Keep changes inside its stated scope; raise a conflict or newly required decision instead of expanding scope silently.
3. Follow repository-specific instructions for tests, formatting, and commands. Report commands and results accurately; distinguish a blocked check from a passing check.
4. At the review point, record what changed, deviations, decisions, and verification in the Task's as-built comment. Update its board status and close it only when its acceptance criteria are met.
5. Reassess the remaining plan when evidence changes. Update the Epic, dependencies, or Tasks transparently before executing work that no longer matches the plan.

## Close

Confirm every Task is complete, the Epic's Definition of Done is verified, and the final change is reviewed and integrated under the repository's normal pull request process. Then update and close the Epic. Follow the tracker guideline for shared issue and board details.
