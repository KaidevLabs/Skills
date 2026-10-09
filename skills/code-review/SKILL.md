---
name: code-review
description: Review a change against the consumer repository's documented standards and its originating specification as separate axes. Use for a diff, branch, completed plan step, or a request such as "review since <ref>".
---

# Code review

Review the change on two independent axes:

- **Standards:** Does the code follow the target repository's documented conventions and quality bar?
- **Spec:** Does the change implement the request or plan step faithfully and stay within its scope?

Keep the reports separate so success on one axis cannot hide a failure on the other. This is an on-demand review, not an implementation-plan step.

## 1. Establish the change

Use the owner's fixed point when supplied: a branch, tag, or commit. For a range, compare the merge base with the current work (`git diff <fixed-point>...HEAD`) and capture the commit list (`git log <fixed-point>..HEAD --oneline`). Confirm the ref resolves and the diff is non-empty before reviewing.

- If a fixed point is supplied and the working tree is dirty, ask whether uncommitted changes are in scope. If included, review the range and the additional working-tree changes (`git diff HEAD` plus untracked files).
- If no fixed point is supplied and the tree is dirty, review staged and unstaged changes (`git diff HEAD`) plus untracked files reported by `git status --porcelain`.
- If no fixed point is supplied and the tree is clean, ask which base to review against. Never guess.

Record the exact diff, commits, and changed-file list once and pass the same scope to each review axis.

## 2. Find the specification

Use sources in this order:

1. The relevant Task or plan step and its parent Epic, if the work follows Kaidev's GitHub tracker. Use the Task's scope, acceptance criteria, test seams, and exclusions plus the Epic's goal, settled decisions, and Definition of Done. The as-built close comment records what happened; it is not the specification.
2. A specification, issue, design, or request supplied by the owner.
3. If no specification is available, report that explicitly and skip the Spec axis.

If the diff spans multiple Tasks or changes files a Task excludes, report the blurred boundary under Spec.

## 3. Find applicable standards

Read the target repository's applicable instructions and standards first: agent instruction files, contribution guides, style and architecture docs, testing guidance, and domain-specific documentation that matches the changed paths. Do not assume any particular file exists. Use the repository's own test, lint, and formatting commands; do not invent commands based on another project.

Alongside documented standards, consider these Fowler-inspired design heuristics as judgement calls, never automatic violations: Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, and Refused Bequest. A documented repository convention overrides a heuristic. Skip issues already enforced by tooling and avoid treating stylistic preference as a finding.

## 4. Review and report

When independent sub-agents are available, delegate Standards and Spec separately so neither review biases the other. Give each the exact change scope and applicable source text. If delegation is unavailable, perform the two passes separately yourself. Delegated results still require aggregation and verification.

The Standards pass reports per-file or per-hunk documented-standard breaches with a citation to the rule, plus any design heuristic as a judgement call. The Spec pass reports missing or partial requirements, unrequested behavior or excluded files, and requirements that appear implemented incorrectly; cite the relevant specification text. Keep each report concise and evidence-based.

Present:

## Standards

<findings, or "No findings.">

## Spec

<findings, or "No spec available." / "No findings.">

Finish with a one-line count of findings on each axis and the most severe finding within each, if any. Do not combine the axes into one ranking.
