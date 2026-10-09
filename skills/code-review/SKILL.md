---
name: code-review
description: Review a change against the consumer repository's standards and originating specification as separate axes. Use for diffs, branches, completed plan steps, or requests such as review since a ref.
---

# Code review

Review two independent axes: **Standards** (does the change follow repository conventions?) and **Spec** (does it implement the request and stay in scope?). Keep the reports separate so success on one cannot hide failure on the other. This is an on-demand review, not an implementation-plan step.

## Establish the change

Use the owner's fixed point when supplied. For a range, use `git diff <fixed-point>...HEAD` and capture `git log <fixed-point>..HEAD --oneline`. Confirm the ref resolves and the diff is non-empty. If a fixed point is supplied and the working tree is dirty, ask whether uncommitted changes are in scope; include `git diff HEAD` and untracked files only when authorized. If no fixed point is supplied and the tree is dirty, review staged and unstaged changes plus untracked files. If the tree is clean, ask which base to use. Never guess. Capture the exact diff, commits, and changed-file list once.

## Find the specification

Use the relevant Task or plan step and parent Epic when work follows Kaidev's GitHub tracker: include scope, acceptance criteria, test seams, exclusions, Epic goal, settled decisions, and Definition of Done. The as-built close comment is a record, not the spec. Otherwise use an owner-supplied issue, design, or request. If none exists, state that and skip the Spec axis. A diff across Tasks or touching explicitly excluded files is a scope finding.

## Find applicable standards

Read the target repository's actual instructions and standards: agent instruction files, contribution guides, style and architecture docs, testing guidance, and domain-specific documents that match changed paths. Do not assume a particular file exists or invent commands from another project.

Also consider these Fowler-inspired design heuristics as judgement calls, never automatic violations: Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, and Refused Bequest. Documented repository conventions override a heuristic. Skip issues already enforced by tooling.

## Review and report

When available, delegate Standards and Spec as separate bounded reviews, giving each the exact scope and relevant source text. Otherwise perform the two passes separately yourself. Verify delegated results and keep the report evidence-based. Standards findings cite the rule and location; heuristic findings are labelled judgement calls. Spec findings cover missing/partial requirements, unrequested behavior or excluded files, and incorrect implementation, citing the requirement.

Present `## Standards` and `## Spec`, each with findings or a clear no-findings/no-spec statement. Finish with the count and most severe finding on each axis. Do not merge or rerank the axes.
