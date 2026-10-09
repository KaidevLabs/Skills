---
name: security-review
description: Audit an application for security weaknesses across trust boundaries and report prioritized, evidence-backed findings. Use for a security review or threat-focused audit; adapt checks to the repository's stack and documented test setup.
---

# Security review

Conduct a read-only security audit unless the owner explicitly requests implementation. Trace realistic attack paths, validate findings against code and configuration, and report actionable evidence. Do not claim a vulnerability from a keyword match alone.

## Scope and approach

1. Establish the target, trust boundaries, sensitive data, exposed interfaces, and any exclusions. Inspect the repository's agent instructions, architecture, deployment, and test documentation first.
2. Build an inventory of entry points and data flows: HTTP/API routes, authentication callbacks, jobs and queues, file uploads, webhooks, command interfaces, administrative actions, and external integrations that exist in the project.
3. Trace untrusted input to sensitive operations. Evaluate relevant categories below; skip those that do not apply and add stack-specific risks when evidence calls for them.
4. Verify candidate findings by following guards, validation, authorization, encoding, persistence, and downstream effects. Use safe, local tests or minimal reproductions only when supported by the repository's setup. Do not exercise production systems or expose secrets.
5. Report findings first, ordered by severity and exploitability, followed by coverage gaps and non-finding hardening ideas. Do not edit application code during an audit.

## Trust-boundary checklist

- Authentication, session handling, account recovery, and identity linking
- Authorization, tenant or organization isolation, and object-level access
- Input validation, injection, unsafe deserialization, and command execution
- Output encoding, cross-site scripting, template injection, and unsafe rendering
- CSRF, CORS, and cross-origin assumptions where browser credentials are involved
- File upload, path traversal, archive extraction, and generated download handling
- SSRF, URL fetching, redirects, DNS changes, and outbound network access
- Webhook signature verification, replay protection, and idempotency
- Secrets, tokens, cryptographic operations, and sensitive data exposure
- Rate limits, resource exhaustion, and abuse controls on costly operations
- Database access, mass assignment, transaction boundaries, and race conditions
- Dependency, deployment, debug, and security-header configuration

## Delegated review

If sub-agents are available and the scope is broad, delegate bounded areas (for example, entry-point inventory, authorization paths, or file and network handling). Provide the repository context and constraints. Ask for paths, exact code evidence, attack preconditions, impact, and a minimal safe validation. The lead reviewer must verify and deduplicate all returned findings. If delegation is unavailable, inspect the areas sequentially.

## Finding format

For each finding include:

- **Severity:** Critical, High, Medium, or Low, with a concise rationale.
- **Location:** exact file and line or symbol.
- **Attack path:** attacker capability and required preconditions.
- **Impact:** what data or operation is affected.
- **Evidence:** the missing or ineffective control and how the path reaches it.
- **Validation:** safe reproduction or test evidence, if available.
- **Remediation:** a focused control that addresses the root cause.

Separate confirmed findings from hypotheses and hardening suggestions. State what was reviewed, what was not reviewed, and which checks could not run. If a likely issue is ruled out by an existing control, cite that control briefly rather than listing a false positive.
