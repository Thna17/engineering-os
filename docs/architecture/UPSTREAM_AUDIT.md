# AGTX Upstream Audit

Audit date: 2026-10-17 (Asia/Phnom_Penh)

Repository: `https://github.com/fynnfluegge/agtx`

Imported commit: `8ae68682f10b05ad77a8d1b307569946d871f814`

Upstream version context: `v1.0.6-3-g8ae6868`

Engineering OS branch: `feat/engineering-os`

## Import and license

The source was cloned into an empty, non-Git directory with complete history. The clone's `origin`
remote was renamed to `upstream`. The Apache License 2.0 file, history, author attribution, and
upstream URL were preserved. The upstream manifest incorrectly declared `license = "MIT"`; this
branch corrects that packaging metadata to `Apache-2.0` to match `LICENSE` and the README.

## Reuse inventory

| Area | Existing implementation | Engineering OS disposition |
|---|---|---|
| Task lifecycle | Backlog, Planning, Running, Review, Done; cycles, dependencies, escalation | Reuse and extend with durable run/attempt and approval records |
| Multi-project state | Global project index plus per-project SQLite databases | Reuse for local mode; introduce repository interfaces before multi-user storage |
| Persistence | SQLite, WAL, busy timeout, additive migrations, transition queue, runtime snapshots | Reuse; add schema-versioned migrations, leases, events, checkpoints, and recovery tests |
| Agent integration | Claude, Codex, Gemini, Cursor, Copilot, Grok, OpenCode, Antigravity, pi | Reuse command/spec registry; add capability declarations and policy envelopes |
| Isolation | Per-task Git worktrees and dedicated tmux windows | Reuse as the local runner; strengthen path validation and add optional sandbox runners |
| Workflow policy | TOML plugins with phase commands, artifacts, gates, agent switching, copy-back | Reuse; evolve into a typed, versioned workflow definition |
| MCP | Local/global JSON-RPC stdio tools, task operations, pane reads, notifications, board wait | Reuse as control API; add scoped capabilities, idempotency keys, and audit identities |
| UI | Ratatui board and optional authenticated PWA | Reuse for local operator experience; do not treat pairing tokens as multi-user auth |
| Git/PR | Worktree lifecycle, diff, commit, push, conflict detection, PR metadata | Reuse; add explicit PR preparation and release-approval state machine |
| Tests | Unit/integration suites for DB, Git, agents, MCP, tmux, TUI, and web | Reuse and extend with crash/restart, policy, workflow, and end-to-end fixtures |
| Skills/plugins | AGTX skills plus OpenSpec, Spec Kit, GSD, BMAD, Superpowers and others | Reuse as adapters; pin and verify external CLI contracts |
| Logging | Daily structured tracing logs | Reuse locally; add correlation IDs, retention controls, redaction, and event export |

## Stable components

The core models, plugin parser, agent-spec registry, worktree abstraction, tmux control interface,
SQLite access layer, MCP transport, dependency graph, and deterministic transition rules are useful
and well covered enough to remain the foundation. The web server already separates queued writes
from TUI execution, uses bearer-style pairing tokens, refuses dirty completion, and binds locally
unless an operator deliberately enables a tunnel.

## Components to extend

- `src/db`: durable workflow runs, attempts, leases, checkpoints, approvals, artifacts, events, and
  a real migration ledger.
- `src/core`: a deterministic workflow reducer independent of TUI polling.
- `src/agent`: capability discovery, supported authentication checks, quota/backoff signals, and a
  runner-neutral protocol.
- `src/git`: PR preparation, immutable base/head evidence, and signed release-approval linkage.
- `src/mcp`: caller identity, per-tool authorization, idempotency, and non-secret structured results.
- `src/config` and plugins: schema versioning, validation, pinned external tool requirements, and
  safe command policies.
- `src/web`: eventually consume an authenticated service API; keep the current PWA local-only.

## Experimental or constrained areas

The README explicitly labels the orchestrator experimental. It depends on a live TUI, tmux idle
heuristics, agent-generated artifacts, and MCP notifications rather than a durable scheduler. Tunnel
spawning is documented in source as not validated on the original author machine. The mobile token
model is appropriate for a single trusted operator, not teams. Initialization and cleanup scripts
execute configured shell text and therefore require a policy boundary before commercial use.

## Missing for Engineering OS

- Durable long-running workflow ownership, leases, heartbeats, retries, cancellation, and restart
  reconciliation.
- A deterministic test gate with immutable command, environment, commit, exit status, and log
  evidence.
- Structured review findings and bounded Gemini repair loops.
- Pull-request preparation as a first-class state and an explicit human release-approval record.
- Safe execution profiles, command allow-lists, sandbox adapters, resource limits, and secret
  scoping.
- Provider quota/rate-limit handling and supported authentication-state diagnostics.
- Tenant, user, project membership, RBAC, audit export, retention, and managed-runner enrollment.
- Artifact storage abstraction and an API boundary suitable for remote runners.

## Extension boundaries

Keep domain transitions in `core`, durable state behind repository traits in `db`, execution behind
a runner interface, provider-specific behavior in agent/tracker adapters, and user interfaces as
clients of an application service. The TUI must stop being the only process capable of advancing
work. This boundary permits the same local SQLite runner to later operate under a managed service
without rewriting Git, plugins, or agent adapters.
