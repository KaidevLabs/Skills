---
name: eureka
description: Discovery protocol for analyzing a problem with the owner before planning or code — use when starting a new feature, bug, or problem that needs decisions settled, or before an Epic is planned.
---

# Eureka

The discovery phase — analyzing a problem with the owner before any plan or work. Use whenever a problem is big or ambiguous enough that deciding wrong costs a session, and always before an Epic is planned.

## Protocol

- Evidence before questions: verify the current state with evidence first — grep, DB, tests, logs. Only ask what the evidence cannot answer. Every current-state claim in the resulting Epic carries its evidence.
- One question at a time. The owner decides; the agent never stands in for the owner's side of the exchange — an agent that answers its own questions has broken the protocol.
- Grill before settling: challenge every decision before it lands in the do-not-revisit table — what does it complicate, what does it rule out, what would prove it wrong. Settled is do-not-revisit; pre-settle is the last cheap place to be wrong out loud.
- Record settled decisions immediately: each one lands in the Epic's **Settled decisions** table (`A# | decision | rationale | date`), marked "do not revisit" — recorded at once in the draft while discovery runs, via issue-body edits (API writes) once the Epic is published. A decision without its rationale is an invitation to relitigate.
- Track what is not yet specifiable: questions you can tell are coming but cannot yet state sharply go to **Not yet specified**. The test is whether the question is sharp, not whether it is answerable — sharp questions become steps, the rest waits.
- Close with **Out of scope** (work ruled beyond the goal — scope, not sharpness, lands it there) and a **Definition of done** checklist whose items are verifiable.
- Close the branches before stopping: for every settled decision, one level out — what hangs on it. Each branch must end recorded: settled (the Epic's Settled decisions table), asked in the session, parked deliberately as a sharp open question (a line in the Epic's **Open branches**), or fog (**Not yet specified**). An opened branch may not silently evaporate — "the way is clear" is something the owner can verify at Epic review, not an agent assertion.

## Close

Stop when the way is clear — the discovery closes by drafting the **Epic body** (Goal, Settled decisions, Current state, Definition of done, Not yet specified, Out of scope; plus **Open branches** when sharp questions are deliberately parked). Execution work hands the draft to planning (the `plans` skill) to add the execution order, the executor rules, and the step Tasks with their wiring, per the tracker doc. Design-only efforts — decisions, no code — publish as a single Epic with no step Tasks; the owner's ratification closes it SETTLED. The pull to start doing the work is the signal the discovery is done.
