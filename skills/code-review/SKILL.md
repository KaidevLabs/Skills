---
name: code-review
description: Review a change against the consumer repository's documented standards and its originating specification as separate axes. Use for a diff, branch, completed plan step, or a request such as "review since <ref>".
---

# Code Review

Two-axis review of the diff between a fixed point and the current work:

- **Standards**: does the code conform to the target repository's documented standards?
- **Spec**: does the code faithfully implement what the originating step Task or spec asked for?

Both axes run as **sub-agents** when delegation is available so they don't pollute each other's context; this skill aggregates their findings. Keep the reports separate so success on one axis cannot hide a failure on the other. This is an on-demand review, not an implementation-plan step: it adds no step to the plan lifecycle and is not required at step close.

## 1. Identify the change

- **Fixed point given** (branch, tag, or SHA): the diff is `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). If the working tree is also dirty, ask whether the uncommitted work is in scope (it then joins the review as `git diff HEAD` plus the untracked files).
- **No fixed point, working tree dirty** (the step-close case — the work is uncommitted): the diff is `git diff HEAD` (staged + unstaged) plus the untracked files from `git status --porcelain`.
- **No fixed point, tree clean**: ask which base to review against. Do not guess.

Capture the diff command once, and for a range the commit list via `git log <fixed-point>..HEAD --oneline`. Before going further, confirm the fixed point resolves (`git rev-parse`) and the diff is non-empty. A bad ref or empty diff should fail here, not inside two sub-agents.

## 2. Identify the spec source

Resolve the spec in this order:

1. **A step Task sub-issue** — if the work follows Kaidev's GitHub tracker, the Task whose `Spec` section overlaps the changed files. The spec is the Task body's `Spec` plus its `not touched in this step` and `Seams under test`, and from its parent **Epic** the `Goal`, the `Settled decisions` the step cites, and `Definition of done`. The as-built resolution comment is a record of what happened, not spec — leave it out. If the diff spans files of more than one step's Task, that is itself a finding (a blurred boundary); surface it under Spec.
2. **A spec the owner passed** — any document or path.
3. **Ask.** If there is no spec, the **Spec** axis is skipped and the report notes "no spec available".

## 3. Identify the standards sources

Read the target repository's applicable instructions and standards first: agent instruction files, contribution guides, style and architecture docs, testing guidance, and domain-specific documentation that matches the changed paths. Do not assume any particular file exists. Use the repository's own test, lint, and formatting commands; do not invent commands based on another project.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Skip anything tooling already enforces — formatters for style, the test suite for behavior — and avoid treating stylistic preference as a finding.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a->b()->c()->d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

## 4. Spawn both sub-agents

If independent sub-agents are available, spawn the two axes concurrently (one message, two `task` calls, general agents) so neither review biases the other. If delegation is unavailable, perform the two passes separately yourself — the same content rules apply.

Give each sub-agent everything it needs; it has no other access to your context: the diff command, the commit list, the changed-file list, and the **full source text of its axis** (the standards sources found in step 3, or the spec text from step 2, pasted in complete). Cap each report at 400 words and require citations.

**Standards sub-agent brief**: "Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent brief**: "Report: (a) changes the spec asked for that are missing or partial; (b) behaviour in the diff the spec didn't ask for — scope creep, including files the Task's exclusion section lists; (c) requirements that look implemented but where the implementation looks wrong. Quote the spec line for each finding. Run the cheap audits the step defines (grep audits); do not run the test suite. Under 400 words."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

## 5. Aggregate

Present the two reports under `## Standards` and `## Spec` headings, verbatim or lightly cleaned. Do **not** merge or rerank findings, because the two axes are deliberately separate (see _Why two axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes: that's the reranking the separation exists to prevent.

## Why two axes

A change can pass one axis and fail the other:

- Code that follows every standard but touches a file the step excludes, or implements a decision the Settled decisions table settled differently → **Standards pass, Spec fail.**
- Code that implements the step exactly as written but breaks the repo's testing conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.
