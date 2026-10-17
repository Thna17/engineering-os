# Engineering OS Product Requirements Document

**Status:** Blueprint baseline
**Owner:** Product and Engineering
**Updated:** 2026-10-17
**Foundation:** AGTX at `8ae68682f10b05ad77a8d1b307569946d871f814`

## Product definition

Engineering OS is a local-first software-engineering control system that turns a product idea into
a reviewed, tested, pull-request-ready change while keeping a human in control of release. It uses
Codex by default for planning, supervision, and review; Gemini CLI by default for implementation and
repair; deterministic runners for quality decisions; and AGTX for projects, tasks, worktrees, tmux,
plugins, SQLite, MCP, TUI, and web access.

The product is successful when an operator can leave a bounded workflow running, restart the
machine or process, return later, and understand exactly what happened, what it cost, which evidence
supports each gate, and what human decision is required next.

## Goals and product outcomes

1. Reduce idea-to-reviewable-PR lead time without hiding engineering decisions.
2. Make autonomous execution recoverable, bounded, observable, and safe on a developer machine.
3. Produce durable product, architecture, plan, test, review, and release evidence.
4. Support multiple repositories and replaceable coding-agent providers.
5. Establish interfaces that can later support a commercial multi-user service without making the
   MVP depend on a hosted control plane.

Success measures for a representative fixture suite:

- At least 90% of valid, dependency-ready tasks reach the next human/actionable state without
  manual session repair (`PM-001`).
- Zero duplicate pull requests or external comments across injected crash/restart tests (`PM-002`).
- 100% of passed quality gates reference current-commit executable evidence (`PM-003`).
- 100% of release approvals are invalidated when the PR head changes (`PM-004`).
- Idle local service memory at or below 250 MiB and scheduler CPU below 2% on the reference low-spec
  machine (`PM-005`).
- A fresh operator can locate a failed step and its evidence in under three minutes (`PM-006`).

## Personas and jobs

The primary Product Owner, Engineering Lead, Developer/Reviewer, Platform Operator, and future
Organization Administrator are defined in [PERSONAS.md](PERSONAS.md). Their core jobs are to convert
intent into testable scope, delegate work safely, judge engineering evidence, recover failed runs,
and control organizational access and spend.

## Functional requirements

| ID | Requirement | MVP |
|---|---|---|
| FR-PLAN-001 | Capture an idea through guided questions and persist problem, users, constraints, outcomes, assumptions, and open questions. | Yes |
| FR-PLAN-002 | Generate a versioned PRD whose requirements and acceptance criteria have stable IDs and provenance. | Yes |
| FR-BLUE-001 | Generate and update a Master Engineering Blueprint linking requirements, components, data, security, milestones, and tests. | Yes |
| FR-ADR-001 | Create, supersede, search, and link architecture decisions; never silently rewrite accepted decision history. | Yes |
| FR-BACKLOG-001 | Generate a dependency-aware implementation backlog with scope, owner role, risk, estimate class, required evidence, and acceptance links. | Yes |
| FR-BACKLOG-002 | Prevent dispatch until hard dependencies and required approvals are satisfied. | Yes |
| FR-AGENT-001 | Delegate tasks to isolated sessions and worktrees with role, capability, policy, budget, and context envelopes. | Yes |
| FR-AGENT-002 | Use Codex as the default planner, supervisor, and reviewer through a replaceable adapter. | Yes |
| FR-AGENT-003 | Use Gemini CLI as the default developer and repair agent through supported CLI authentication. | Yes |
| FR-AGENT-004 | Support optional provider adapters without provider-specific state in the workflow core. | Yes |
| FR-RUN-001 | Execute agent and deterministic commands through a runner contract with workspace, environment, network, timeout, output, CPU, memory, and disk limits. | Yes |
| FR-RUN-002 | Provide host-local and Docker isolation profiles; clearly label their guarantees and availability. | Yes |
| FR-GATE-001 | Define ordered objective gates for format, static analysis, build, unit, integration, security, license, and project-specific checks. | Yes |
| FR-GATE-002 | Bind every result to repository, commit, command digest, tool versions, timestamps, status, and retained logs. | Yes |
| FR-REVIEW-001 | Persist structured review findings with stable ID, priority, fingerprint, location, evidence, and disposition. | Yes |
| FR-REPAIR-001 | Run selected repairs in bounded cycles, retest changed code, deduplicate findings, and escalate exhausted cycles. | Yes |
| FR-GIT-001 | Manage branch/worktree lifecycle and prepare or update a draft pull request idempotently. | Yes |
| FR-GIT-002 | Reconcile remote branch, PR, and check state after restart before repeating an external mutation. | Yes |
| FR-DEPLOY-001 | Invoke an explicit staging deployment adapter, capture environment/revision/URL/health evidence, and support rollback commands. | Later MVP increment |
| FR-REL-001 | Require human release approval bound to project, environment, PR, head SHA, evidence set, approver, and time. | Yes for PR approval; deployment later |
| FR-DASH-001 | Show multiple projects, workflow state, current work, blockers, spend, required action, and recent failures in TUI and existing web UI. | Yes |
| FR-MEM-001 | Maintain project memory as versioned, attributable facts, decisions, constraints, summaries, and artifact references with retention controls. | Yes |
| FR-REC-001 | Persist runs, steps, attempts, leases, checkpoints, events, and idempotency keys for crash-safe background execution. | Yes |
| FR-REC-002 | Reconcile stale leases and external effects at startup, then resume, retry, or request intervention deterministically. | Yes |
| FR-COST-001 | Enforce per-run/per-project concurrency, attempt, wall-time, token/cost estimate, disk, and provider quota budgets. | Yes |
| FR-COST-002 | Pause safely on rate limits or exhausted budgets and expose the next reset/action without bypassing provider controls. | Yes |
| FR-MON-001 | Schedule opt-in maintenance workflows such as dependency checks, health checks, stale-branch review, and backup verification. | Later MVP increment |
| FR-PLUGIN-001 | Load schema-versioned plugins that declare commands, artifacts, capabilities, permissions, gates, and compatible core versions. | Yes |
| FR-PLUGIN-002 | Validate and show the effective plugin policy before execution; reject unknown privileged permissions. | Yes |
| FR-TENANT-001 | Preserve domain/repository interfaces needed for future organizations, users, RBAC, remote runners, metering, and tenant-scoped storage. | Design only |

## Non-functional requirements

| ID | Requirement and measure |
|---|---|
| NFR-REL-001 | Committed domain state survives process or host restart; SQLite transactions preserve invariants under injected crashes. |
| NFR-REL-002 | External writes are at-least-once attempted but effectively-once observed through idempotency and reconciliation. |
| NFR-REL-003 | Scheduler restart recovery begins within 30 seconds of service availability for up to 100 active tasks. |
| NFR-SEC-001 | Default execution grants no secrets or network access except those explicitly declared by policy. |
| NFR-SEC-002 | Agent content cannot modify its policy envelope or mark its own gate passed. |
| NFR-SEC-003 | Secrets and recognized credential patterns are redacted from logs, events, prompts, MCP responses, and artifacts. |
| NFR-PERF-001 | On a 4-core/8-GB machine, the idle service uses ≤250 MiB RAM, ≤2% CPU, and ≤1 GiB product state excluding repositories/artifacts. |
| NFR-PERF-002 | Default concurrency is one agent and one validation job on machines with <8 GiB available RAM; operators may raise it explicitly. |
| NFR-UX-001 | Every blocked/failed state reports cause, evidence, retry safety, and available operator actions. |
| NFR-PORT-001 | Core local operation supports current macOS and Linux; Windows is supported through WSL until native runner parity is proven. |
| NFR-COMP-001 | Workflow definitions and event payloads are schema-versioned and backward compatible for one prior minor release. |
| NFR-OBS-001 | Every run, step, attempt, external call, approval, and policy decision carries correlation IDs and structured timestamps. |
| NFR-LIC-001 | Release builds produce dependency/license inventories and preserve Apache-2.0 attribution and third-party notices. |

## Product-level acceptance criteria

| ID | Testable criterion | Requirements |
|---|---|---|
| AC-P1-001 | From a plain-text idea, a fixture run creates a PRD and blueprint with stable IDs, assumptions, acceptance criteria, and linked backlog items. | FR-PLAN-001/002, FR-BLUE-001, FR-BACKLOG-001 |
| AC-P1-002 | An accepted ADR remains immutable; a change creates a superseding ADR and all affected backlog links are discoverable. | FR-ADR-001 |
| AC-P2-001 | A dependency-blocked task is never dispatched; completion of all hard dependencies makes it eligible on the next scheduler tick. | FR-BACKLOG-002, FR-REC-001 |
| AC-P2-002 | Killing the process at each attempt boundary and restarting produces no lost run and no duplicate recorded effect in the crash matrix. | FR-REC-001/002, NFR-REL-001/002 |
| AC-P2-003 | A malicious task requesting a broader command, path, environment, network, or secret capability is denied and audited. | FR-RUN-001, NFR-SEC-001/002 |
| AC-P3-001 | The reference fixture executes Codex plan → Gemini implementation → deterministic checks → Codex review → Gemini repair with adapter mocks and bounded retries. | FR-AGENT-002/003, FR-GATE-001, FR-REVIEW-001, FR-REPAIR-001 |
| AC-P3-002 | Every green gate references current commit evidence; stale evidence becomes invalid after a commit changes. | FR-GATE-002, PM-003 |
| AC-P3-003 | Repeated review findings keep stable IDs/fingerprints; exceeding configured repair cycles produces human escalation. | FR-REVIEW-001, FR-REPAIR-001 |
| AC-P4-001 | Two retries of PR preparation create one draft PR and reconcile its head/check state. | FR-GIT-001/002, PM-002 |
| AC-P4-002 | Approval succeeds only for an authorized human and current head SHA; a new head invalidates it. | FR-REL-001, PM-004 |
| AC-P4-003 | A staging adapter records deployment and health evidence and can invoke its declared rollback without exposing credentials. | FR-DEPLOY-001, NFR-SEC-003 |
| AC-P5-001 | TUI and web clients show consistent multi-project actionable state from the same application service. | FR-DASH-001 |
| AC-P5-002 | A low-spec benchmark meets NFR-PERF-001/002 and pauses work before resource budgets are exceeded. | FR-COST-001, NFR-PERF-001/002 |
| AC-P5-003 | Backup/restore into a clean profile preserves project memory, run history, evidence metadata, and pending approvals. | FR-MEM-001, NFR-REL-001 |
| AC-P7-001 | Commercial discovery produces approved, measured concurrency, tenant, availability, retention, region, runner-trust, and cost requirements before infrastructure selection. | FR-TENANT-001 |
| AC-P7-002 | Before multi-user release, authorization and tenant-isolation tests prove users, runners, secrets, records, and artifacts cannot cross project/organization scope. | FR-TENANT-001, NFR-SEC-003 |

## Subscription and provider restrictions

- Engineering OS may launch only official/provider-supported CLIs or APIs and may inspect only
  documented authentication status. It must not extract browser cookies, subscription tokens, local
  credential databases, or session traffic.
- A consumer subscription does not imply API, automation, resale, concurrency, or unattended-use
  rights. The operator is responsible for a plan that permits the selected use; the adapter must
  display relevant limitations without claiming legal entitlement.
- Rate limits, quota errors, account suspension, interactive authentication, and terms-required
  confirmation are terminal or waiting states, never reasons to rotate identities or evade controls.
- Secrets stay in provider-approved stores or operator-configured secret references. Engineering OS
  stores metadata and opaque references, not raw long-lived credentials.

## Licensing and commercial distribution

AGTX history and Apache-2.0 obligations remain intact. Optional MIT integrations remain separate
processes unless an ADR approves incorporation. Before each distribution, generate a dependency
SBOM/license report, review reciprocal or noncommercial terms, preserve NOTICE/LICENSE material,
document modifications, verify trademarks and provider branding, and confirm that packaged CLIs may
be redistributed. Commercial features must not remove source-license rights from the open-source
core. Legal review remains required before distribution; this blueprint is an engineering control,
not legal advice.

## Explicit non-goals for the initial MVP

Hosted multi-tenancy, autonomous production deployment, unrestricted arbitrary shell automation,
general-purpose CI replacement, proprietary model hosting, built-in billing, and support for every
issue tracker are outside the initial MVP. See [MVP_SCOPE.md](MVP_SCOPE.md).

## Principal risks and tradeoffs

| Risk / tradeoff | Consequence | Treatment |
|---|---|---|
| SQLite favors a local single writer over horizontal control-plane scale | Future hosted migration may be required | Repository boundaries, measured M7 discovery, ADR before replacement |
| tmux and provider CLIs are pragmatic but their output/protocols can change | Session detection and resume may break | Capability probes, adapters, contract fixtures, supported-version matrix |
| Host execution works on low-spec machines but provides weaker isolation | Malicious code can affect the user account | Mandatory policy, visible risk, Docker option, no unattended high-risk mode |
| Docker improves isolation but consumes substantial disk/RAM | Low-spec hosts can exhaust resources | Preflight, pinned limits, serial defaults, host fallback, managed-cache cleanup |
| Local artifacts simplify MVP but need retention and backup | Disk growth or missing files can block evidence | Quotas, manifests, integrity checks, operator-controlled retention |
| Bounded repair may stop before resolution | Human escalation remains necessary | Clear cycle/cost caps, preserved findings/evidence, resumable handoff |
| Default Codex/Gemini roles create provider exposure | Auth, quota, terms, or product changes can pause work | Replaceable adapters, mockable contracts, no provider logic in core |
| Extensive documentation can drift from code | False confidence in requirements | `EOS-0004` trace checker and documentation gates in CI |
