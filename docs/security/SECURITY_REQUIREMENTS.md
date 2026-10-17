# Security Requirements

These requirements operationalize `THREAT_MODEL.md`. “Must” items block the affected milestone.
Security controls fail closed and produce redacted audit evidence.

## Identity, authorization, and approvals

| ID | Requirement | Verification |
|---|---|---|
| SEC-IAM-001 | Every mutation records an actor kind/ID; anonymous actors may read only local static assets and explicitly public health data. | API/application authorization tests |
| SEC-IAM-002 | Local pairing/session tokens are random, revocable, excluded from logs after bootstrap, and never represented as multi-user identity. | Route/log/revocation tests |
| SEC-IAM-003 | Future network users/services use authenticated identities and deny-by-default project/tenant RBAC at the service boundary. | Design-only until M7; tenant matrix before release |
| SEC-IAM-004 | Release approval requires authorized human identity and exact subject, head, evidence, policy, environment, time, and decision. Agents/runners cannot hold this role. | AC-P4-002 matrix |
| SEC-IAM-005 | Manual overrides require a human actor, reason, bounded scope/expiry, visible risk, and append-only audit. They cannot fabricate passed evidence. | Override/audit tests |

## Agent and prompt boundaries

| ID | Requirement | Verification |
|---|---|---|
| SEC-AGT-001 | Treat repository text, issues, retrieved content, terminal output, plugin artifacts, and model output as untrusted data. | Prompt-injection fixtures |
| SEC-AGT-002 | Prompts describe granted capabilities; enforcement occurs outside the prompt and cannot be changed by agent output. | Policy abuse matrix |
| SEC-AGT-003 | Planner/reviewer cannot mark gates passed, alter policy/budgets, approve release, or access implementation write scopes unless separately assigned. | Role-capability tests |
| SEC-AGT-004 | Developer/repairer writes only its canonical assigned worktree and declared artifact outputs. | Traversal/symlink/worktree tests |
| SEC-AGT-005 | Repair receives selected findings and relevant context; unrelated changes are detected and surfaced. | Diff-scope tests |

## Command, filesystem, and isolation

| ID | Requirement | Verification |
|---|---|---|
| SEC-EXE-001 | Commands use executable/argument arrays; shell interpolation is denied unless an approved immutable script artifact is explicitly allowed. | Metacharacter/property tests |
| SEC-EXE-002 | Canonicalize workspace and every writable/mounted path; reject traversal, symlink escapes, special files, project root, home, and broad system paths. | Abuse matrix |
| SEC-EXE-003 | Destructive Git/worktree operations resolve a registered Engineering OS target, inspect dirty state, and never target the primary worktree. | Git safety integration tests |
| SEC-EXE-004 | Every effect has timeout, cancellation, process-tree cleanup, output limits, and backend resource limits. | Hung/forked-child/output tests |
| SEC-EXE-005 | Docker uses pinned image digests, minimal mounts, non-root user when compatible, dropped capabilities, PID/CPU/memory limits, and explicit network mode. | Container configuration tests |
| SEC-EXE-006 | Host mode visibly states reduced isolation; mandatory policy cannot be disabled by an agent or plugin. | UI and policy tests |

## Secrets, subscriptions, and providers

| ID | Requirement | Verification |
|---|---|---|
| SEC-SEC-001 | Store opaque secret/profile references only; resolve secrets at the narrowest adapter/runner boundary and omit them from child environments by default. | Environment capture tests |
| SEC-SEC-002 | Redact recognized patterns and declared secrets before logs/events/artifacts/MCP/UI persistence; record redaction occurrence. | Canary-secret suite |
| SEC-SEC-003 | Never print, return, embed in user-visible identifiers, or include credentials in process arguments/URLs. | Process/log inspection tests |
| SEC-PRV-001 | Use supported CLI/API authentication. Browser-token scraping, undocumented credential access, token sharing/resale, and quota/account rotation are prohibited. | Static review and adapter tests |
| SEC-PRV-002 | Normalize authentication, quota, rate-limit, suspension, and terms-required states; obey retry-after and request operator action when needed. | Fake-provider matrix |
| SEC-PRV-003 | Do not infer that consumer subscription access permits automation, API use, concurrency, redistribution, or service-bureau use. | Product copy review |

## Network and external effects

| ID | Requirement | Verification |
|---|---|---|
| SEC-NET-001 | Network is denied for deterministic gates unless declared; allowed destinations/protocols are policy data. | Network policy tests |
| SEC-NET-002 | Local UI/API binds loopback by default. Wider exposure is explicit, authenticated, audited, and warns that agent control grants code-execution authority. | Binding/startup tests |
| SEC-NET-003 | PR, comment, deployment, and rollback calls have persisted intent/idempotency and reconcile remote state before retry. | Failure-injection provider tests |
| SEC-NET-004 | Production merge/deploy operations are unavailable to autonomous MVP adapters. | Capability contract tests |

## Data, audit, and supply chain

| ID | Requirement | Verification |
|---|---|---|
| SEC-DAT-001 | SQLite foreign keys, integrity constraints, checksummed migrations, restrictive permissions, and verified backups protect authoritative state. | Migration/permission/restore tests |
| SEC-DAT-002 | Artifact digests detect tampering/missing content; untrusted HTML/ANSI/Markdown is escaped or sanitized by each UI. | Tamper/rendering tests |
| SEC-DAT-003 | Audit records cover policy decisions, external effects, approvals, overrides, and secret-redaction events and are append-only through application APIs. | Repository/API tests |
| SEC-DAT-004 | Retention/deletion operates by project/profile and never deletes outside validated managed roots. | Boundary/restore tests |
| SEC-SUP-001 | Commit lockfiles and toolchain/container pins; release produces SBOM, license inventory, vulnerability scan, and provenance where supported. | Release pipeline |
| SEC-SUP-002 | Plugin enablement verifies schema, compatibility, digest/source, tools, permissions, and licensing; installation grants nothing. | Malicious plugin fixtures |
| SEC-SUP-003 | Third-party code/assets require ADR, license review, notice update, maintainer, and modification record. | Documentation/release check |

## Availability and cost abuse

| ID | Requirement | Verification |
|---|---|---|
| SEC-AVL-001 | Enforce concurrency, attempts, wall time, disk, output, and estimated cost/token budgets before and during execution. | AC-P5-002 |
| SEC-AVL-002 | Reserve disk safety margin and pause before critical exhaustion; cache cleanup targets only validated managed paths. | Low-disk tests |
| SEC-AVL-003 | Rate-limit API/MCP mutations and bound query/log pagination; malformed clients cannot create unbounded work. | Load/fuzz tests |
| SEC-AVL-004 | Backup, restore, cancellation, and scheduler recovery work without provider/model access. | Offline operational tests |

## Security release gates

Runner, policy, identity, secret, forge, deployment, migration, and public-network changes are high
risk. They require threat-model impact, abuse tests, structured review with no unresolved P0/P1, and
human approval. Multi-user/public hosted release additionally requires independent penetration
testing, tenant-isolation tests, incident response, key rotation, audit export, and restore drills.
