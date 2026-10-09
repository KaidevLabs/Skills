# Kaidev Skills

Reusable AI agent skills and working guidelines for KaidevLabs repositories. This repository is the source of truth for the shared documents; each consumer repository opts in by copying only the folders it needs.

## Skills

| Skill | Use it when | Scope |
| --- | --- | --- |
| [code-review](skills/code-review/SKILL.md) | Reviewing a diff against repository standards and its originating specification. | Any language; uses repo-local standards and optional specs. |
| [eureka](skills/eureka/SKILL.md) | Discovering requirements and settling decisions before planning implementation. | Any project. |
| [plans](skills/plans/SKILL.md) | Turning settled scope into an ordered GitHub Epic and Task plan, then tracking execution and closure. | Kaidev's shared GitHub Projects workflow. |
| [resource-first-controllers](skills/resource-first-controllers/SKILL.md) | Designing Laravel controllers around resource actions and explicit routes. | Laravel only. |
| [security-review](skills/security-review/SKILL.md) | Auditing trust boundaries and producing prioritized, evidence-backed findings. | Any stack; adapt checks to the repo. |
| [sequential-implementation-orchestration](skills/sequential-implementation-orchestration/SKILL.md) | Executing a multi-step plan one reviewed step at a time. | Projects with an available sub-agent/delegation mechanism. |
| [tdd](skills/tdd/SKILL.md) | Implementing behavior in small red/green vertical slices. | Any stack; use the repo's test conventions. |

## Guidelines

- [GitHub tracker](guidelines/tracker-github.md): shared Epic, Task, dependency, and project-board conventions.
- [Agent delegation](guidelines/agents.md): boundaries for sub-agents and orchestration ownership.
- [Scratch files](guidelines/scratch.md): where temporary work belongs and how to clean it up.

## Copy skills into a project

1. Choose the skill folders relevant to the work. Copy each selected `skills/<name>/` directory into the consumer repository's established agent-skills location (for example, `.ai/skills/` or `.agents/skills/`). Keep the skill folder name and `SKILL.md` filename intact.
2. Copy only the referenced shared guidelines needed by those skills into the consumer's documentation location, or retain them in a shared, readable location. `plans` requires the GitHub tracker guideline; orchestration and review use the agent-delegation guideline; scratch-file advice is optional unless referenced by local conventions.
3. Review each `SKILL.md` against the target repository. Follow that repository's own architecture, commands, test instructions, terminology, and documented standards. Skills intentionally do not prescribe project paths or test commands.
4. Commit the copied files with the project's normal workflow. To refresh later, compare the selected folders with this repository and copy updates deliberately.

Do not copy every skill by default: Laravel guidance applies only to Laravel repositories, and the plans skill assumes Kaidev's GitHub Projects conventions. The other skills can be adapted to a repository's stack and tooling.

## Source and maintenance

These documents are maintained for KaidevLabs and are adapted from existing team practices. Improvements should remain reusable across repositories: avoid project-specific identifiers, paths, branches, secrets, or commands unless clearly marked as examples.
