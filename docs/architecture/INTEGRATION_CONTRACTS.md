# Integration Contracts

All contracts use semantic schema versions. Unknown optional fields are ignored; unknown required
capabilities fail before execution. Logs and error text are untrusted and redacted before storage or
display. Contract fixtures are maintained for the current and previous supported minor version.

## Runner contract (`runner.v1`)

```json
{
  "request_id": "uuid",
  "idempotency_key": "run/step/attempt/effect",
  "workspace": {"root": "/canonical/worktree", "mode": "read_write"},
  "command": {"executable": "cargo", "args": ["test", "--locked"], "shell": false},
  "environment": {"allow": ["PATH", "CARGO_HOME"], "secret_refs": []},
  "network": {"mode": "deny", "destinations": []},
  "limits": {"wall_seconds": 1800, "output_bytes": 10485760, "memory_mib": 2048, "cpus": 2, "disk_mib": 4096},
  "inputs": {"commit_sha": "hex", "policy_digest": "sha256", "toolchain_digest": "sha256"}
}
```

Result status is `succeeded`, `failed`, `timed_out`, `cancelled`, `policy_denied`, or
`infrastructure_error`; it includes timestamps, exit/signal, normalized resource usage, stdout/stderr
artifact digests, runner identity/version, isolation mode, and observed commit. A runner rejects
shell strings unless a separately approved policy explicitly permits a fixed script artifact.

## Agent adapter (`agent.v1`)

Required operations: `probe`, `start`, `send`, `observe`, `cancel`, and optionally `resume`.
`probe` returns installed version, supported roles, structured-output/resume/hooks capabilities,
authentication state (`ready`, `required`, `expired`, `unknown`), and quota state without token
material. A session request contains role, worktree, prompt artifact digest, read/write scopes,
allowed tools, runner policy, budget, and correlation IDs.

Normalized outcomes: `working`, `waiting_for_input`, `completed`, `blocked`, `rate_limited`,
`authentication_required`, `failed`, `cancelled`, and `lost`. Agent “success” does not advance an
objective gate. Codex and Gemini command construction remains in their adapters and must use only
documented CLI flags and supported authentication.

## Quality gate definition (`gate.v1`)

```yaml
key: rust-test
kind: test
command:
  executable: cargo
  args: [test, --locked, --features, "serve test-mocks"]
policy: deterministic-no-network
timeout_seconds: 1800
requires: [rust-build]
invalidated_by: [source, manifest, lockfile, toolchain, policy]
required_for: [review, pull_request]
```

Gate status is derived from current evidence. Waivers require authorized human actor, reason,
expiration, scope, and risk; a waiver never changes failed evidence into passed evidence.

## Review finding (`finding.v1`)

Fields: stable `id`, fingerprint over category/path/range/normalized claim, priority `P0..P3`,
category, title, explanation, evidence, source location, requirement refs, reviewer attempt, status,
and confidence. Status is `open`, `selected`, `repairing`, `resolved`, `accepted_risk`, `invalid`, or
`superseded`. Only deterministic recheck or authorized disposition closes a finding.

## Workflow plugin manifest (`workflow.v2` target)

Plugins declare core compatibility, roles, ordered steps/DAG, input/output artifacts, gate keys,
external tool pins, permissions, resource estimates, retry policy, and UI metadata. Installation is
inert; enabling records the manifest and policy digests after validation. A plugin cannot request
unknown permission names, raw credential values, wildcard project roots, or automatic release.
Legacy AGTX TOML plugins load through a compatibility mapper and receive a high-trust warning until
their shell hooks are converted to structured runner requests.

## Local application API

MVP exposes typed Rust service methods to TUI/web/MCP. HTTP routes remain under the existing Axum
server and use JSON resource versions, `Idempotency-Key`, and `If-Match`/version on mutations.
Representative resources are `/api/projects`, `/runs`, `/steps`, `/attempts`, `/evidence`,
`/findings`, `/approvals`, `/budgets`, `/memory`, and `/commands`. Local static assets are public as
today, while data/action routes require the local session token. This is not a multi-user API.

## Domain events (`event.v1`)

Event envelope:

```json
{
  "event_id": "uuid",
  "sequence": 42,
  "type": "attempt.completed.v1",
  "aggregate": {"type": "workflow_run", "id": "uuid", "version": 7},
  "actor": {"kind": "runner", "id": "local-docker"},
  "correlation_id": "uuid",
  "causation_id": "uuid",
  "occurred_at": "2026-10-17T07:00:00Z",
  "payload": {}
}
```

Core event families cover artifact accepted, task eligible/blocked, run/step/attempt state, lease
expired, budget reserved/exhausted, gate evaluated/invalidated, finding recorded/dispositioned,
PR reconciled, deployment observed, approval granted/invalidated, memory superseded, and policy
denied. Events contain references/digests rather than raw logs or secrets.

## Git and forge contracts

Local Git operations use explicit repository/worktree paths and argument arrays. Destructive cleanup
checks that a target is a registered Engineering OS worktree and never the primary worktree.

The forge port provides `probe_repository`, `find_or_create_draft_pr`, `update_pr`, `get_pr`,
`list_checks`, and optional `comment`. Idempotency identity is project + branch + workflow run;
create first searches/reconciles. GitHub MVP uses `gh` or documented API authentication and stores
only repository/PR IDs and opaque credential profile references. Merge is outside the autonomous
adapter contract.

## Deployment contract (`deployment.v1`)

Adapters implement `plan`, `deploy_staging`, `status`, `health`, `logs`, and `rollback`. Requests bind
environment, repository, commit/image digest, approved manifest, secret references, timeout, and
idempotency key. Production deployment and promotion are unavailable in MVP. Staging credentials
cannot be passed to agent processes; the adapter resolves them at effect execution.

## Subscription integration restrictions

Adapters must not scrape browser cookies, read undocumented credential stores, proxy consumer
subscriptions to other users, cycle accounts to evade quotas, suppress provider usage reporting, or
claim that a subscription permits automation. Interactive login remains provider-owned. A quota or
terms error is normalized and surfaced. Commercial packaging links to provider terms and requires
operators to supply appropriately licensed accounts/API access.

## Compatibility and error model

Errors include stable code, class, safe message, retryability, retry-after, operator action, and a
correlation ID. Provider text is nested/redacted, never used as a stable code. Breaking contract
changes require a new major version, migration tooling, fixtures, and ADR.
