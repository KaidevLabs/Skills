# GitHub tracker conventions

Kaidev plans work in GitHub using an **Epic** issue for an outcome and **Task** sub-issues for independently executable steps. The repository's GitHub Project is the planning surface. Use GitHub's native sub-issue and `blocked by` relationships to show scope and ordering; do not duplicate the plan in project-specific files unless the owner asks for that.

## Find the project

Resolve the target repository from the current work context (for example, its Git remote). Select the Kaidev organization project whose title matches that repository's name, ignoring case. Confirm there is exactly one match before editing the board. If there is no unique match, stop and ask the owner; do not guess, create a project, or hard-code project IDs in reusable instructions.

## Issue and board conventions

- Use Epic and Task issue types for the planned workflow. Each Task is a sub-issue of its Epic; represent dependencies with native `blocked by` relationships.
- Backlog is the default state. A Ready-like column means the owner has reviewed and accepted the task for execution. Move a Task to the active column only when execution starts; move an Epic to a review column when its pull request is ready. Close completed Tasks and move them to Done. Follow the actual board's column names when they differ.
- Do not rely on Priority, Size, or labels unless the organization has enabled and agreed their use. Adding an issue to a project may leave its status unset; set the intended status explicitly.
- Keep implementation order in the Epic's execution table and dependency edges in GitHub. The table is readable summary; the native links are authoritative for blocking.

## Epic template

```markdown
## Goal
<observable outcome and why it matters>

## Settled decisions
| Decision | Choice | Rationale |
| --- | --- | --- |

## Current state
<relevant behavior, evidence, constraints, and source references>

## Execution order
| Step | Task | Blocked by |
| --- | --- | --- |

## Definition of Done
- [ ] <verifiable outcome>

## Not yet specified
- <open question or decision that must be resolved before the affected step>

## Out of scope
- <explicit exclusions>

## Executor rules
- Follow the task boundary and repository instructions; stop if a prerequisite or contract is missing.
```

Add an **Open branches** section only when concurrent work is deliberately allowed. For each Task, state the parent Epic, a focused specification, what must not be touched, exact verification commands (resolved from the consumer repository's documented setup), and relevant test seams. Keep unrelated tasks separate.

## Create and execute a plan

1. Use discovery to clarify the goal, constraints, evidence, settled decisions, open questions, and out-of-scope work before creating implementation tasks.
2. Draft the Epic and ordered Task breakdown. Ensure each Task has one coherent result, a bounded file/behavior scope, and independently verifiable acceptance criteria. Create sub-issue and dependency relationships in GitHub.
3. Add the Epic and Tasks to the repository's matching project, then set their statuses explicitly. Check that the project board, execution order, and `blocked by` graph agree.
4. Start only an unblocked, accepted Task. Keep each executor on one Task at a time unless the Epic explicitly allows independent parallel work. Do not begin a blocked Task because it appears convenient.
5. At Task close, report what changed, verification run and results, deviations, and decisions made during review. Record the implementation commit or pull request as applicable, then close the Task and update the Epic and board.
6. Close the Epic only after all Tasks and the Definition of Done are satisfied and the resulting pull request has been reviewed and integrated through the repository's normal merge process.

## Operational notes

Use the authenticated GitHub UI or `gh` commands available in the environment. Resolve repository and project context at runtime; never copy numeric project, issue, or database IDs from one repository into reusable documentation. Prefer issue references in prose and use commit trailers only when they are correct for the repository's merge policy. Do not use a closing keyword that would close the Epic before the planned review and merge.
