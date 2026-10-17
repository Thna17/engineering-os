# Component Design and Ownership

## Target module map

The names below are target boundaries; extraction should be incremental to avoid destabilizing
AGTX. Existing modules remain in place until their behavior is covered by the new port.

| Component | Primary responsibility | Owns | Must not own | Initial AGTX reuse |
|---|---|---|---|---|
| `core::workflow` | Pure run/step/attempt state and transition rules | States, commands, events, invariants | SQLite, tmux, Git, network | Extend `core::actions` and task transitions |
| `application` | Use cases and authorization/policy orchestration | Command handlers, queries, DTOs | Provider-specific calls | Extract shared logic from TUI/MCP/web |
| `scheduler` | Lease ready work and reconcile attempts | Tick, queue ordering, retry/backoff, recovery | UI state, agent prompts | Replace experimental TUI-owned polling incrementally |
| `db` repositories | Transactional persistence and migrations | Aggregates, leases, events, outbox, indexes | Domain transition decisions | Extend existing rusqlite/WAL layer |
| `policy` | Resolve effective permissions and budgets | Capability sets, command profiles, grants/denials | Executing commands | Wrap plugin config and agent trust rules |
| `runner` | Execute structured requests | Host/Docker backends, limits, cancellation, log capture | Workflow progression | Reuse worktree/tmux operations behind port |
| `agent` | Normalize coding-agent capabilities and sessions | Codex/Gemini/other adapters, auth diagnostics, resume | Core workflow state | Extend `AgentSpec` registry |
| `quality` | Resolve gates and evaluate runner results | Gate definitions, invalidation, evidence status | Running raw commands directly | New service using runner port |
| `review` | Finding inventory and bounded repair policy | Findings, fingerprints, selection, cycles | Test truth, release approval | New, informed by Ralph Review |
| `git` | Repository/worktree/branch operations | Git status/diff/commit/push/reconciliation | PR provider credentials | Reuse existing AGTX Git modules |
| `forge` | Pull-request provider contract | GitHub adapter, PR/check normalization, idempotency | Local Git internals | Extend existing PR metadata/gh use |
| `deploy` | Staging deployment contract | Deploy/status/rollback normalization | Human approval | New optional adapter boundary |
| `memory` | Verified project knowledge | Facts, decisions, summaries, sources, supersession | Hidden model memory | New persisted domain service |
| `artifacts` | Content-addressed files and retention | Blobs, metadata, redaction status | Domain semantics | Evolve phase artifacts/copy-back |
| `observability` | Logs, audit, metrics, diagnostics | Correlation, redaction, health | Business decisions | Extend tracing logs |
| `tui`, `web`, `mcp` | User/client interfaces | Rendering, input validation, protocol mapping | Alternate state machines | Reuse all existing surfaces |

## Application service operations

Commands: `create_idea`, `revise_artifact`, `accept_plan`, `create_run`, `pause_run`, `resume_run`,
`cancel_run`, `retry_attempt`, `select_findings`, `approve_release`, `reject_release`, `enable_plugin`,
`set_budget`, and `request_staging`. Queries: project summaries, run timeline, task DAG, attempt detail,
evidence, findings, effective policy, budgets, provider health, memory, and approval status.

Every mutation accepts `actor`, `expected_version`, and `idempotency_key`. A version conflict returns
current state and performs no effect. Local mode uses `actor.kind=local_user`; future network mode
uses authenticated user/service identities.

## Durable scheduler

Ready selection is deterministic: eligible state, hard dependencies complete, required approvals
valid, retry time reached, budgets available, concurrency slot available, and no active lease.
Ordering is priority, dependency-unblock value, creation time, then stable ID. One transaction claims
an attempt by setting `lease_owner`, `lease_expires_at`, and incrementing its version.

Retry classes:

- `transient`: bounded exponential backoff with jitter.
- `rate_limited`: wait until provider reset/retry-after within budget.
- `authentication`: no automatic retry; operator action required.
- `policy_denied`: terminal until policy/config revision.
- `deterministic_failure`: route to configured repair or escalation.
- `unknown_external_effect`: reconcile before any retry.

## Planning and blueprint service

Planning uses versioned Markdown artifacts with YAML metadata containing artifact ID, version,
source idea, producing run/attempt, input digests, status, and requirement IDs. Generation occurs in
a planning worktree or artifact directory; human acceptance controls eligibility. Regeneration uses
an impact set, preserving accepted sections that were not affected and never silently rewriting
accepted ADRs.

## Project memory

Memory is explicit data, not unbounded chat history. A `MemoryEntry` is `fact`, `constraint`,
`decision_summary`, `pattern`, or `operational_note`; it includes source artifact/event, confidence,
creator, timestamps, tags, visibility, and supersession. Only verified artifacts, accepted decisions,
runner evidence, or human entries can become authoritative. Agent summaries remain proposed until
verified. Retrieval uses metadata/FTS first; embeddings are an optional adapter and never required.

## Resource manager

Before dispatch it reserves declared concurrency, estimated memory, disk, wall time, and provider
budget. It samples disk/process health while active and requests graceful cancellation before hard
limits. Cost is an estimate unless the provider supplies authoritative usage. Estimates and actuals
are stored separately. Lack of usage data is `unknown`, not zero.

## UI extension

The existing PWA and TUI gain project/run timeline, dependency view, evidence/finding panels,
budgets, and approval actions. Existing routes call application queries and enqueue typed commands.
The current single-user pairing mechanism remains local-mode only. No React rewrite or separate API
server is introduced for MVP.
