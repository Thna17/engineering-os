# Personas and Jobs to Be Done

## P-01 — Product Owner / Founder

**Context:** Owns outcomes and priorities, may not know the repository architecture.
**Job:** “When I have a product idea or change request, help me turn it into testable scope and a
reviewable delivery plan so I can make decisions before engineering cost is committed.”

Needs guided discovery, explicit assumptions, scope boundaries, progress in product language, and a
clear release decision. Must control acceptance and release; must not be asked to approve raw shell
commands. Success means tracing each delivered change to an accepted requirement.

## P-02 — Engineering Lead / Principal Engineer

**Context:** Owns architecture, quality, risk, and throughput across repositories.
**Job:** “When autonomous agents work in parallel, give me durable decisions, dependency ordering,
objective evidence, and bounded recovery so I can supervise systems rather than terminals.”

Needs ADRs, component ownership, policy review, architecture drift signals, structured findings,
cost controls, and escalation queues. Success means failures are diagnosable and no agent bypasses a
gate or release policy.

## P-03 — Developer / Reviewer

**Context:** Implements difficult changes, reviews generated changes, and takes over escalations.
**Job:** “When automation hands me work, preserve the reasoning, diff, failing evidence, and session
context so I can continue without reconstructing history.”

Needs isolated branches, reproducible commands, concise handoff notes, finding locations, and manual
override paths that are audited. Success means taking over or returning a repaired task safely.

## P-04 — Local Platform Operator

**Context:** Installs Engineering OS on a workstation or build host and keeps it healthy.
**Job:** “When workflows run for hours, keep resource use bounded, recover after restarts, protect
credentials, and tell me what operational action is required.”

Needs diagnostics, backup/restore, health, disk/concurrency budgets, supported authentication,
retention, and clear isolation guarantees. Success means predictable operation on 4 cores/8 GB RAM.

## P-05 — Organization Administrator (future)

**Context:** Manages teams, projects, runners, policies, audit, and spend in a commercial service.
**Job:** “When several teams share the service, enforce tenant and project boundaries and show who
authorized each material action.”

Needs SSO/OIDC, RBAC, tenant-scoped secrets and storage, runner enrollment, quotas, audit export,
retention, and billing attribution. This persona informs interfaces but is outside MVP delivery.

## Anti-personas and abuse cases

- An agent attempting to self-authorize commands, secrets, merges, or deployments.
- A plugin author assuming installation implies unrestricted host access.
- A user attempting to bypass subscription quotas or harvest provider credentials.
- A remote caller attempting to use the local web pairing mechanism as team authentication.

These actors receive denial, bounded access, and audit records rather than convenience shortcuts.
