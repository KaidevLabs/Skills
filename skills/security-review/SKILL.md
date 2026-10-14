---
name: security-review
description: Adversarial security audit of the application. Phase 1 surveys the app's trust boundaries and produces a vector list; Phase 2 runs one sub-agent per vector (static trace + PoC test); Phase 3 aggregates findings into GitHub Bug issues and a hardening Epic. Use when the owner asks for a security review or audit of the app. Laravel projects using Sail.
---

# Security Review

Adversarial audit of the application in its current state. Output: a set of GitHub **Bug** issues — one per verified finding (or per grouped finding, the owner's call at triage) — and a hardening **Epic** that cites them. Every verified finding is backed by a PoC test under `tests/Security/`, which stays in-repo. The Bug issues are the backlog; the Epic is the plan that resolves them.

## Prime directive

Find and prove vulnerabilities. Do not fix anything.

- No existing file is modified (app code, config, routes, migrations, `.env`, deps). No git mutations — the owner reviews the tree and commits (git workflow).
- The only new files: `tests/Security/*Test.php`. Findings become GitHub issues; nothing is written to a local report file.
- A suspicion you cannot prove goes in the relevant Bug issue as `SUSPECTED — needs manual verification` with the reasoning. Never silently drop a suspicion.

## Rules of engagement

- All commands via `vendor/bin/sail`. Local environment only — never production.
- A failed command or downed service: troubleshoot it yourself first (`vendor/bin/sail up -d`, `vendor/bin/sail logs`, read the code). A genuine blocker is recorded under **Assumptions & limitations** — and confirmed with the owner before it shapes a finding.
- Work through the whole inventory; every vector gets a deep-dive, every class a recorded outcome.
- PoC tests are feature tests using `RefreshDatabase`, named `*Test.php`, one file per vector: `tests/Security/V<n>-<kebab-slug>Test.php`.
- The testing DB is shared — every `artisan test` run re-migrates it and concurrent runs corrupt each other. Deep-dive sub-agents bypass the shared DB: run each PoC on its own database (create it first: `vendor/bin/sail mysql -e 'CREATE DATABASE IF NOT EXISTS sec_<n>'`):
  `vendor/bin/sail shell -c 'DB_DATABASE=sec_<n> php vendor/bin/phpunit tests/Security/V<n>-<slug>Test.php'` (phpunit.xml sets `DB_DATABASE` without `force`, so the env var wins).
- `tests/Security` sits outside the default Feature suite (`./tests/Feature/*`), so the audit suite never disturbs the main suite and vice versa.
- Fake everything external: `Http::fake()`, `Mail::fake()`, `Notification::fake()`, `Bus::fake()`, `fake()`. Prove the app's logic is exploitable; do not burn third-party quota.
- Project test conventions apply: `fake()` helper, capture the created id from the HTTP response (MySQL `created_at` is second-granular — do not `latest()` a just-created row), `makeUserSubscribed` for subscription gating.
- Scratch goes in `.tmp/`.
- If a fresh test run mass-fails right after `migrate:fresh`, re-run before chasing — the testing DB corrupts intermittently.
- Duster runs on the new test files only (new files, not the repo) before the audit closes.

## Vector classes — what to look for

The stable checklist: twelve trust-boundary classes. Phase 1 finds *instances* of these classes in this codebase; the instance list drives Phase 2. A class with no instance in the app is recorded as "surveyed — no instance", not skipped.

1. **Input → storage**: validation coverage vs what actually persists — mass assignment, missing `max:`/type rules, oversized or deeply nested payloads, encoding bombs.
2. **Input → state transitions**: who moves an order/payment/status — verified server-side against the source of truth, or trusted from client input? Replay, race/TOCTOU, idempotency.
3. **Money integrity**: amounts, currency, price source (request body vs stored config), negative/zero/overflow, double spend, fee/tax application.
4. **Identity & authentication**: every auth mechanism — brute force, user enumeration, token/code entropy, expiry, single-use, session fixation.
5. **Authorization & object access**: IDOR on object-scoped routes — is the principal verified against the object? Id predictability.
6. **Async surface**: jobs/events/listeners/schedule — dispatch sites, privileges, replayability, flooding (queue capacity, SMS credits, third-party quota), seed/dev jobs reachable from outside.
7. **External dependencies**: outbound calls and inbound webhooks — does a third-party payload flip app state? Secrets protection, user-controlled URLs (SSRF).
8. **Files & storage**: uploads (MIME, size, filename, traversal, accessibility), exports (path traversal, IDOR), cache.
9. **Resource exhaustion**: throttle coverage per entry point; unbounded per-request work (external lookups, search, N+1, unbounded pagination, regex DoS).
10. **Injection**: raw SQL, `orderBy` fed by input, concatenated queries, serialization sinks, stored XSS into admin UI, open redirects.
11. **Exposure & hygiene**: debug tooling in non-local, `APP_DEBUG`, `.env` access, committed secrets, keys in built assets, verbose errors, admin-panel auth, CSRF.
12. **Data lifecycle**: destruction without authorization (delete, mass update/delete), soft-delete resurrection, leakage — what do anonymous responses reveal?

## Methodology

**Phase 1 — boundary survey (main agent).** Inventory every trust boundary in the app:

- every route (api + web) and the middleware applied (auth, throttle, billing gates);
- inbound callbacks/webhooks;
- every outbound HTTP client call and how its response is handled;
- every job/event/listener and schedule entry: dispatch sites, privileges, replayability;
- every authentication mechanism (session auth, Socialite OAuth, any token/link mechanisms) — inventory what the app actually uses;
- storage surfaces: uploads, exports, cache, queue;
- the config/secrets surface: env, config rows, committed secrets, debug tooling;
- how tests authenticate (TestCase, seeders, link tokens) — needed for Phase 2 briefs.

Output: the **vector list** — one row per attack vector: `V# | class | surface (endpoint/job/integration) | trust boundary crossed | the concrete question | files to trace`. Instances only — every row points at something real in this codebase. The vector list becomes the hardening Epic's attack-surface inventory appendix. The owner reviews the vector list before Phase 2 spawns — the only cheap steering point: surfaces added, vectors dropped or re-prioritized there.

**Phase 2 — deep-dive (one sub-agent per vector).** General sub-agents, spawned in batches of 5. Each brief carries: the vector row, the full rules of engagement and test conventions pasted in (the sub-agent has no other access), its DB name `sec_<n>`, and its PoC filename. Work: static trace first (read the code, do not guess), then a focused PoC if the hypothesis holds, then run it. Returns, per finding: status (VERIFIED / SUSPECTED), evidence (exact request/response excerpt), the PoC test name and its result — plus an **adjacent observations** section for anything outside the vector's scope. A finding counts as **VERIFIED** only with a passing PoC demonstrating the exploit. Sub-agents stay in their lane: adjacent observations are triaged in Phase 3, where a follow-up vector may be added and deep-dived in a subsequent batch. A blocked sub-agent documents the blocker in its result and returns — it never stalls the audit.

**Phase 3 — aggregate (main agent).** Deduplicate — two vectors can land on the same root cause; keep one PoC, note both surfaces. Re-run the entire `tests/Security` suite once on the default DB to confirm it passes as a whole. Then file the findings as GitHub issues and an Epic (conventions and gh recipes in the [tracker doc](plans/tracker-github.md) shipped with the `plans` skill):

1. **One Bug issue per finding (or per grouped finding).** Each Bug issue carries: title, severity, ease of exploitation, impact, status (VERIFIED/SUSPECTED), file:line or endpoint, a short remediation *direction* (conceptual — do not implement), and the PoC test name + result excerpt. A finding counts as VERIFIED only with a passing PoC demonstrating the exploit; a SUSPECTED finding notes what blocks proof. Triage assigns each finding a disposition: **fix** (an Epic step), **risk accepted** (with rationale), or **defer** (with what would force the revisit).
2. **The hardening Epic.** A new Epic issue on the project board citing the finding Bug issues (as sub-issues or dependencies, the owner's call) as the work it resolves. Its `Goal` = close the findings; the rest of its body carries the report cover that used to live in a local file: the executive summary (3–5 worst findings in plain business language + counts by severity), the methodology & scope, the findings table (id, title, severity, ease, impact, status, surface, disposition — each row links its Bug issue), the attack-surface inventory (the Phase 1 vector list and route/middleware map), the assumptions & limitations, and the PoC index (every `tests/Security/` file and what it proves). Per-finding detail lives on the Bug issues only — the Epic indexes, it does not store.
3. **Adjacent observations.** Anything a Phase 2 sub-agent surfaced outside its vector's scope is triaged here: a follow-up vector may be added and deep-dived in a subsequent batch, or logged as its own Bug issue.

## From findings to Epic (the board lifecycle)

Findings ride the board columns, not a local status line: **Bug** issues go Backlog → Ready → In progress → Done; the hardening **Epic** flows Backlog → Ready → In progress → In review → Done (PR-driven at Epic close). See the [tracker doc](plans/tracker-github.md) shipped with the `plans` skill for the conventions (run its resolution block first — the doc records no ids) and gh recipes.

- Fixed, accepted, and deferred findings stay recorded on their Bug issue — that is the issue's memory. There is no separate archive file; the Bug issue is closed when its finding is fixed or formally accepted, and a deferred finding is terminal for the Epic — its Bug stays open on the board carrying the revisit trigger, and the Epic closes once every cited finding is fixed, accepted, or deferred.
- When the owner works the findings: an Epic citing the Bug issues is created (the `plans` lifecycle — eureka first if any decision is open). The Epic's steps are the fixes, grouped per "divide cleanly or not at all". Each fix step's verification exercises the finding's PoC test — flipped to assert the fixed behaviour, then moved into the regular suite. `tests/Security/` holds live audit artifacts only.

## Repeat reviews

A new audit files new Bug issues. Previous findings are context, not a checklist: the new Epic's body carries a **Prior findings** section referencing each previous finding's Bug issue (`#NN`) and its current status (re-verified present / fixed / risk accepted).

## Severity

Critical — unauthenticated free paid order, data destruction, RCE, auth bypass. High — unauthenticated job triggering, IDOR on orders/exports, missing rate limits on row-creating or external-API endpoints, upload abuse. Medium — enumeration, weak tokens, validation gaps requiring chaining. Low — hardening gaps.

## Definition of done

- Every vector in the Phase 1 list was deep-dived; every class 1–12 has an outcome (vectors found, or "surveyed — no instance") in the hardening Epic's attack-surface inventory.
- Every VERIFIED finding has a passing PoC test in `tests/Security/` referenced in its Bug issue, and a corresponding Bug issue (or grouped issue) on the board.
- The entire `tests/Security` suite passes in one run on the default DB.
- No existing file modified; no git mutations.
