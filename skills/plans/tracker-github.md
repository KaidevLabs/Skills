# GitHub tracker conventions

How planning works in repos on this workflow: work is planned as **Epic issues**, and every Epic carries its steps as **Task sub-issues**. The project board is the only planning surface. This doc is the conventions reference every planning skill points at.

It records **no ids** — no repo slug, board number, field id, or column option id. Every value is discovered from invariants at execution time (resolution below), so the doc never goes stale and reads identically in any repo.

## Resolution — run once per session, before any board write

Everything the recipes need derives from two invariants: the cwd's git remote (gh resolves owner/repo itself for `gh issue` and `gh api`), and one naming convention — **the board is the org project titled after the repo** (case-insensitive). If the match is not exactly one project, stop and ask.

```bash
OWNER=$(gh repo view --json owner --jq .owner.login)
REPO=$(gh repo view --json name --jq .name)

# Board — the org project titled after the repo; exactly one match, else stop and ask
PROJECT_JSON=$(gh project list --owner "$OWNER" --format json \
  | jq -c --arg r "$REPO" '(.projects // .)[] | select((.title | ascii_downcase) == ($r | ascii_downcase))')
PROJECT=$(jq -r .number <<<"$PROJECT_JSON")     # board number  → item-add, field-list, item-list
PROJECT_ID=$(jq -r .id <<<"$PROJECT_JSON")      # board node id → item-edit

# Status field + column option ids — resolved by name, never hardcoded
gh project field-list "$PROJECT" --owner "$OWNER" --format json \
  | jq -c '.fields[] | select(.name == "Status") | {field_id: .id, options: [.options[] | {name, id}]}'
```

Hold the printed ids for the session; every `item-edit` below takes them (`$STATUS_FIELD_ID`, `$STATUS_OPT_*`). Column **roles** are fixed; names vary per board — map by pattern, and confirm with the owner when a pattern is ambiguous:

| Role | Name pattern | Meaning | Set by |
|---|---|---|---|
| Backlog | `backlog` | published Epic awaiting owner review; the project's default column | publisher |
| Ready | `ready` / `sprint` | owner's sprint-ready decision | owner |
| In progress | `in progress` | claimed via `Execute #NN` | executor |
| In review | `in review` | PR open — Epics only, the only In-review meaning | executor |
| Done | `done` | closed | executor |

Columns matching no role (e.g. archive columns) are unmodeled — the machinery never writes them.

## Board

- The board is the org project titled after the repo (resolution above). The Status field is a single select; its option ids come from the resolution, never from this doc.
- Projects-v2 Priority/Size fields stay dormant. Labels carry no category meaning.
- Step Tasks ride the project unclaimed until claimed: `item-add` never sets Status; a fresh item lands in the project's default column — the Backlog role.
- Claim = Task → In progress. Done = closed, at close — Tasks never park in In review.
- The frontier is never a column: it is the open Tasks with no live `blocked_by` edges, read against the Epic's execution-order table.

## Types

| Type | Meaning |
|---|---|
| Epic | a plan |
| Task | a plan step (sub-issue of its Epic); also the ad-hoc default |
| Bug / Feature | ad-hoc issues |
| untyped | not machinery |

- Every plan step is a Task sub-issue, always — no checkbox step lists inside an Epic body.
- Labels play no category role; issue-level Priority applies once an issue is typed.

## Issue anatomy

### Epic body — an index, not a store

Sections, in order:

1. **Goal** — what reaching the end looks like.
2. **Settled decisions** — the `A# | decision | rationale | status | date` table; `ratified` rows are do-not-revisit, `proposed` rows are provisional and open to revision.
3. **Current state** — the evidenced facts the plan builds on.
4. **Execution order** — `Step | Task | Blocked by` table; dependency edges mirrored as native `blocked_by`. Updated at every step close via API writes.
5. **Definition of done** — checklist; an item is checked only with verifiable evidence.
6. **Not yet specified** — in-scope fog that graduates into Tasks as the work advances.
7. **Out of scope** — work ruled beyond the goal.
8. **Open branches** (optional) — sharp questions the discovery deliberately parked; present only when one exists.
9. **Executor rules** — at minimum: the project's command runner, per-step verification + lint, and the linking + close protocol below.

Step detail lives in the Task; the Epic only indexes it. Epic upkeep is API writes: fetch the body, apply the edit, write it back.

### Task body

```markdown
## Parent
#<epic>

## Spec
<what changes, where — "not touched in this step" for everything adjacent>

## Verification
<the exact commands / grep audits that prove the step done>

## Seams under test
<behavior-changing steps only: the public boundaries the step's tests exercise>

## Blocked by
<name (#NN), one per line>
```

## Reads

Reads go through the `tasks` tool, never `gh`: an issue, its comments and its hierarchy are read with `tasks read <number>`, and the board is searched with `tasks search "<text>"`, whose qualifiers include `type:`, `state:` and `column:`.

Every recipe under `## Recipes` stays authoritative for writes, and the resolution block still runs once per session before any board write. `gh issue view` is the fallback, not the default — correct only when the tool cannot (no GitHub repository, `gh` not authenticated, plugin absent).

## Recipes

```bash
# issue CRUD — plans publish typed: Epic for the plan, Task for each step (`--type` takes the type name; untyped = ad-hoc)
# gh resolves owner/repo from the cwd's git remote — no --repo flag anywhere
gh issue create --type Epic --title "…" --body-file epic-body.md
gh issue create --type Task --title "NN …" --body-file task-body.md
gh issue create --title "…" --body-file body.md
gh issue comment 33 --body-file comment.md
gh issue edit 32 --body-file body.md
gh issue close 33 --reason completed

# database id — every sub-issue/dependency write takes the REST integer id, never #numbers
# (GraphQL issue JSON carries the node id I_kwDO…, which the REST endpoints reject)
gh api repos/{owner}/{repo}/issues/33 --jq .id

# sub-issue wiring (child db id under the Epic)
gh api -X POST repos/{owner}/{repo}/issues/32/sub_issues -F sub_issue_id=<child db id>
gh api repos/{owner}/{repo}/issues/32/sub_issues
gh api -X DELETE repos/{owner}/{repo}/issues/32/sub_issue -F sub_issue_id=<child db id>

# blocked_by edge — "#34 is blocked by #33": on the CHILD, reference the BLOCKER
gh api -X POST repos/{owner}/{repo}/issues/34/dependencies/blocked_by -F issue_id=<blocker db id>
gh api repos/{owner}/{repo}/issues/34/dependencies/blocked_by
gh api -X DELETE repos/{owner}/{repo}/issues/34/dependencies/blocked_by/<blocker db id>

# project placement — add the item, leave Status unset (prints the item id)
gh project item-add "$PROJECT" --owner "$OWNER" --url <issue url>

# item id lookup — when the item-add output is not at hand
gh project item-list "$PROJECT" --owner "$OWNER" --format json \
  | jq -c '.items[] | select(.content.number == 33) | {id, url: .content.url}'

# claim: Task → In progress
gh project item-edit --project-id "$PROJECT_ID" --id <item id> \
  --field-id "$STATUS_FIELD_ID" --single-select-option-id "$STATUS_OPT_IN_PROGRESS"
# close: Task → Done
gh project item-edit --project-id "$PROJECT_ID" --id <item id> \
  --field-id "$STATUS_FIELD_ID" --single-select-option-id "$STATUS_OPT_DONE"

# Epic branch — first claim only: creates plan/<slug> on the remote LINKED to the Epic issue, then
# checks it out; the step's PR then links to the Epic on its own. gh reuses an existing linked
# branch, so repeating the command is safe. --base defaults to the repo's default branch.
gh issue develop 32 --name plan/<slug> --checkout

# linked-branch check — `gh issue develop --list 32` prints only on a TTY, so use this instead
# (prints the linked branch names; empty output means not linked):
gh api graphql -f query="{repository(owner:\"$OWNER\",name:\"$REPO\"){issue(number:32){linkedBranches(first:5){nodes{ref{name}}}}}}" \
  --jq '.data.repository.issue.linkedBranches.nodes[].ref.name'
```

Scratch files for `--body-file` payloads go in `.tmp/`.

## Linking

- The Epic ⇄ work relation is the **linked branch**, not a closing keyword: the branch is created with `gh issue develop` at the first claim, and the PR opened from it links to the Epic automatically — the issue's Development panel shows the branch, then the PR. Nothing has to be added to the PR body for that.
- At most one code commit per step — a verification-only step has none; the message is suffixed `(#NN)` — never `Closes #NN` or any other auto-close keyword. The keyword is not what closes an Epic anyway: the close is explicit (see Execution mechanics).
- Everywhere outside commit messages, refer to issues by name: `01 tracker-conventions (#33)`, not a bare `#33`.
- AI-posted comments carry no disclaimer (private repo).

## As-built — the resolution comment

Posted on the Task before closing it:

- **What happened** — the outcome, with evidence.
- **Deviations** — from the Spec or a settled decision (write "none" if none).
- **Mid-review owner decisions** — anything settled during the review gate.
- **Commit** — the step's code commit sha; for a verification-only step, the no-code outcome (what was verified, no commit made).

## Execution mechanics

- Branch per Epic: `plan/<slug>` (Epic title, kebab-cased), created at the **first claim** with `gh issue develop <epic> --name plan/<slug> --checkout` — which creates it on the remote already linked to the Epic issue and checks it out — every step commit lands there. Later claims are a plain `git checkout plan/<slug>`.
- The branch is cut from the remote base, not the local one: commits sitting on a local copy ahead of `origin/<base>` do not enter it. Divergence is reported to the owner, never merged in silently.
- Claim (`Execute #NN`): Task → In progress; create the linked branch if absent, check it out if not. If the Epic is not already In progress, it moves too.
- A code repo with no remote (the config repo) can carry neither a linked branch nor a PR: fall back to `git checkout -b plan/<slug>`, and the Epic closes without one.
- One Task per executor, in execution order, honoring `blocked_by` edges.
- Close, on the owner's stage signal: commit the step's code `(#NN)` — a verification-only step skips straight to the as-built comment, which records the no-code outcome → post the as-built comment → close the Task (Done at close) → update the Epic's execution-order row and Definition-of-done items via API writes.
- Resume: `git log` on the branch plus the Epic's open/closed sub-issues reconstruct all state; the frontier is the lowest open unblocked Task.
- Epic close: push the branch → PR to the repo's base branch → Epic In review → merge → Epic Done/closed. The PR links to the Epic by itself through the branch's link; merging does not close the issue on its own, so the close and the Done move stay explicit. PR feedback rides PR comments; sub-issues never reopen.
- After a claim or a close, the panel's column is stale until `r` or the next refresh — the board is correct, the panel is a snapshot.
