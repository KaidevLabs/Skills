<div align="center">

  <a href="https://kaidev.io/">
    <img src="https://kaidev.io/images/isotype_gradient.svg" width="42" alt="Kaidev">
  </a>

  <h1 align="center">Evolve your software with purpose</h1>

  <p>
    <a href="https://kaidev.io/#philosophy"><img src="https://img.shields.io/badge/Philosophy-Kaizen-ea580c?style=flat-square" alt="Philosophy: Kaizen"></a>
    <a href="https://kaidev.io/#about"><img src="https://img.shields.io/badge/Value-Technical%20excellence-16a34a?style=flat-square" alt="Value: Technical excellence"></a>
    <a href="https://kaidev.io/#contact"><img src="https://img.shields.io/badge/Focus-Let%27s%20talk-2563eb?style=flat-square" alt="Focus: Let's talk"></a>
  </p>

  <h3 align="center">
    Kaidev is a software consultancy specializing in development and code refactoring.
    Guided by Kaizen, we strengthen architecture, performance, and automation to turn
    technical challenges into a competitive advantage.
  </h3>

  <p>
    <a href="https://kaidev.io/">Website</a> ·
    <a href="https://www.linkedin.com/company/kaidev/">LinkedIn</a> ·
    <a href="https://bsky.app/profile/jpascual.kaidev.io">Bluesky</a>
  </p>

</div>


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

- [Coding style](guidelines/coding-style.md): PHP/Laravel house standards — imports, architecture, model structure, senior practices, and test conventions.
- [Testing](guidelines/testing.md): how tests are executed in Sail projects, plus shared test-organization conventions.
- [Duster](guidelines/duster.md): the formatting workflow — Duster wraps Pint; never run Pint directly.
- [Agent delegation](guidelines/agents.md): boundaries for sub-agents and orchestration ownership.
- [Scratch files](guidelines/scratch.md): where temporary work belongs and how to clean it up.
- [Browser tests](guidelines/browser.md): pest-plugin-browser conventions and Playwright pitfalls.

Note: the `pest`, `php`, and `enforce-tests` guideline files seen in projects are generated automatically by `laravel boost install` in each repository (adapted to that project's detected tooling) — they are intentionally not maintained here.

The [plans](skills/plans/SKILL.md) skill carries its own [GitHub tracker doc](skills/plans/tracker-github.md) — the shared Epic, Task, dependency, and project-board conventions it and other skills reference — inside its skill folder.

## Copy skills into a project

1. Choose the skill folders relevant to the work. Copy each selected `skills/<name>/` directory into the consumer repository's established agent-skills location (for example, `.ai/skills/` or `.agents/skills/`). Keep the skill folder name and `SKILL.md` filename intact.
2. Skills carry their companion docs inside their own folder: `plans` includes the GitHub tracker doc, which `plans` and `security-review` reference relatively — copy it together with the skill folder. Copy the shared guidelines under `guidelines/` that the copied skills reference (orchestration and review use the agent-delegation guideline; scratch-file advice is optional unless referenced by local conventions).
3. Review each `SKILL.md` against the target repository. Follow that repository's own architecture, commands, test instructions, terminology, and documented standards. Skills intentionally do not prescribe project paths or test commands.
4. Commit the copied files with the project's normal workflow. To refresh later, compare the selected folders with this repository and copy updates deliberately.

Do not copy every skill by default: Laravel guidance applies only to Laravel repositories, and the plans skill assumes Kaidev's GitHub Projects conventions. The other skills can be adapted to a repository's stack and tooling.

## Source and maintenance

These documents are maintained for KaidevLabs and are adapted from existing team practices. Improvements should remain reusable across repositories: avoid project-specific identifiers, paths, branches, secrets, or commands unless clearly marked as examples.

## License

MIT — see [LICENSE](LICENSE).
