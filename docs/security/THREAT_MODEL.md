# Engineering OS Threat Model

## Scope and assets

Engineering OS executes untrusted agent suggestions against source repositories. Assets include
source/history, credentials, developer files, build artifacts, provider quota, CI/CD authority,
workflow integrity, audit evidence, and—later—tenant data. Initial trust boundary is one local
operator and explicitly enrolled project roots. Managed deployment adds users, APIs, databases,
object storage, and remote runners.

## Actors and trust assumptions

- Human Product Owner/release approver: trusted for scoped product and release decisions.
- Agents and model output: untrusted input, including tool arguments, code, Markdown, terminal
  output, repository instructions, and retrieved web content.
- Project repositories and dependencies: potentially malicious.
- External CLIs/providers: authenticated through supported mechanisms but fallible, rate-limited,
  and possible supply-chain targets.
- Local runner host: trusted in phase one; containers reduce blast radius but are not a complete
  security boundary on every platform.

## Primary threats and controls

| Threat | Required controls |
|---|---|
| Prompt injection causes dangerous commands | Structured runner requests, executable/argument policies, no agent-controlled approval expansion, human gate for policy change |
| Filesystem escape or destructive Git action | Canonical workspace roots, symlink/path checks, isolated worktrees, deny broad recursive deletion, backups and recoverable cleanup |
| Credential theft or disclosure | Secret references, minimum child environment, redaction, no prompt/token logging, supported OAuth/device flows, rotation and revocation |
| Unrestricted network exfiltration | Per-step network profiles, default denial in sandboxed validation, destination policy and audit where network is needed |
| Dependency/tool substitution | Lockfiles, exact versions and integrity checks, trusted registries, provenance/notice review, controlled updates |
| False test/review success | Runner-generated immutable evidence, exit status and timeout semantics, commit binding, separate reviewer and fixer, retest after repair |
| Duplicate side effects after crash | Idempotency keys, durable attempts, leases, external-state reconciliation, bounded retries |
| Agent merges or deploys without consent | PR-ready terminal state, explicit human approval bound to head SHA, approval invalidation on change |
| Provider quota/auth bypass | Supported APIs/CLIs, backoff and quota surfacing; prohibit browser-token scraping and evasion |
| MCP or web caller exceeds authority | Local binding by default, authenticated callers, scoped capabilities/RBAC, request limits, audit identity |
| Cross-tenant access | Tenant keys on every row/object, authorization at service boundary, scoped runner credentials, isolation tests |
| Log/artifact leakage | Redaction, access control, retention limits, encrypted transport/storage for managed mode |

## Safety invariants

1. An agent cannot broaden its own execution or secret access.
2. A workflow cannot report a check passed without runner evidence for the current commit.
3. A changed PR head has no valid prior release approval.
4. Recovery does not silently duplicate externally visible actions.
5. Local services bind to loopback unless an operator selects and authenticates exposure.
6. Multi-user mode is unavailable until identity, authorization, tenant isolation, and audit tests
   meet the acceptance criteria.

## Open risks

Current AGTX plugin init/cleanup scripts are shell strings; worktree cleanup includes forced removal;
the experimental orchestrator uses high-trust agent execution; pairing tokens are single-operator
credentials; and tmux panes can contain secrets. Engineering OS must place these behind the safe
runner/policy layer before advertising autonomous unattended operation.

## Verification plan

Add adversarial tests for traversal/symlinks, prompt-injected arguments, secret redaction, command
denial, timeouts/output limits, crash points, stale leases, idempotent PR operations, approval
invalidation, malformed MCP calls, and tenant-object access. Run dependency and license scanning in
CI. Review this model at each architecture phase and before any public network deployment.
