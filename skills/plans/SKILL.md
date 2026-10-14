---
name: plans
description: Plan lifecycle on GitHub — publishing an Epic with its step Task sub-issues, executing a step (`Execute #NN`), closing a step or an Epic. Use whenever Epics, Task sub-issues, or plan steps on the board are created, executed, or closed. Laravel projects using Sail.
---

# Plans

A plan is an **Epic issue** on the project board; every plan step is a **Task sub-issue** of its Epic, wired with native `blocked_by` dependency edges. The board is the only planning surface. The conventions — board resolution, Epic and Task body anatomy, gh recipes, linking, the as-built comment, execution mechanics — are authoritative in the [tracker doc](tracker-github.md) shipped next to this skill; this skill is the lifecycle over that model. The tracker doc records no ids: run its resolution block once per session before any board write.

Lifecycle: **Eureka** (discover) → **Planning** (publish the Epic and its step Tasks) → **Executing** (one step at a time) → **Epic close** (close-step Task → PR → merge). One Epic per feature/issue/bug.

## Eureka

Run the Eureka protocol (the `eureka` skill). Its output is the Epic's Goal, Settled decisions, Current state, Not yet specified, Out of scope, and Definition of done — the Epic-body anatomy the tracker doc fixes, an index, not a store.

## Planning

1. Draft the Epic body and one Task body per step — Parent, Spec ("not touched in this step" for everything adjacent), Verification, Seams under test for behavior-changing steps, Blocked by.
2. Publish: create the Epic (typed Epic), then each Task (typed Task), wiring every Task as a sub-issue of the Epic with database ids; `item-add` every issue to the board — no explicit status edit; the item starts in the project's default column (the Backlog role).
3. Wire each Task's native `blocked_by` edges from the execution-order table — the graph is the order, nothing else marks it (recipes in the tracker doc).
4. Move the Epic to the Backlog role — the publisher's column.

No code in the planning session; the owner reviews the published plan in the GitHub UI, and execution starts only on `Execute #NN`.

Design-only efforts (decisions, no code) are a single Epic with no step Tasks — published like any other; they close SETTLED when the owner ratifies (a resolution comment records the ratification before closing).

## Executing — one step at a time

Claim (`Execute #NN`): move the Task → In progress; if the Epic is not already In progress, it moves too; check out the Epic branch `plan/<slug>` — the Epic title, kebab-cased — created at the first claim, where every step commit lands.

Resume protocol: branch checked out, tree clean, the previous steps' code commits exist (`git log`) and their sub-issues are closed with as-built comments (`gh issue view <n> --comments`) — then implement only that step.

Behavior-changing steps with declared seams are driven by the `tdd` skill: red → green in vertical slices at the declared seams, the single test file after every cycle, the step's full verification at close. Steps without seams follow their Verification directly. Run the step's verification plus Duster before asking for review; there are no mid-step commits — the owner reviews and stages the diff, and that signal is the close signal.

Step close — on the owner's stage signal:

1. commit the code — plain repo-style message suffixed `(#NN)`; a verification-only step has no commit and records `Commit: none — verification-only` in its as-built comment;
2. post the as-built resolution comment on the Task — what happened, deviations, mid-review owner decisions, the commit sha (or `Commit: none — verification-only`);
3. close the Task — Done at close; Tasks never park in In review;
4. update the Epic via API writes — the execution-order row and every Definition-of-done item the step satisfies; a decision settled mid-review settles into the Settled decisions table the same way.

Mid-Epic edits are API writes: fetch the body, apply the edit, write it back. A deviation from a settled decision → stop and confirm with the owner first.

The frontier is never a column: it is the open Tasks with no live `blocked_by` edges, read against the Epic's execution-order table. Only the owner promotes (Ready); an executor claims (In progress) and closes (Done).

## Execution mechanics

- One step per executor: a single dedicated agent (subagent or fresh session), one at a time, in execution order, honoring `blocked_by` edges. A sub-agent executor never mutates git or issues — it returns its work to the orchestrator, which runs the close protocol.
- The right context: the Task body + the Epic's Settled decisions + repo conventions must give an executor everything it needs to work without hunting or guessing. The plan author guarantees it — exact file lists, the changes, full verification commands, and "not touched in this step" for everything adjacent. An executor left to guess makes the decisions the plan should have made, and takes more steps to get there.
- Specify the what, not the how: a Task's Spec says what changes, where, and how you'll know it's done — not line-by-line how. Repo conventions cover the how.
- Divide cleanly or not at all: when the work is one logical unit that doesn't divide cleanly, it stays one step. An honest large step beats two entangled ones.

## Epic close

Every **execution** Epic ends with a close-step Task: the final verification (usually the full suite) and the Definition-of-done audit. Design-only Epics have no steps and take no close step — they close SETTLED on the owner's ratification, as above. A close-step Task's close follows the step-close rule — a code commit when it has code; often it is verification-only and has none — then:

1. push the Epic branch;
2. open the PR to the base branch — the Epic moves to In review, the only In-review meaning; PR feedback rides PR comments, sub-issues never reopen;
3. on merge, close the Epic — Done.
