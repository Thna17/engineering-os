# MVP Scope

## MVP outcome

On one trusted local machine, one operator can manage multiple local Git projects and drive a
recoverable Codex-supervised/Gemini-implemented workflow from an accepted PRD/backlog to one draft
pull request with deterministic evidence and a mandatory human release gate.

## Included

- Versioned idea brief, PRD, blueprint, ADR, and dependency backlog artifacts (`FR-PLAN-*`,
  `FR-BLUE-001`, `FR-ADR-001`, `FR-BACKLOG-*`).
- AGTX TUI and existing web UI extended through one application service (`FR-DASH-001`).
- SQLite durable runs, steps, attempts, leases, events, artifact metadata, memory, findings,
  evidence, idempotency, budgets, and approvals (`FR-REC-*`, `FR-MEM-001`).
- Codex and Gemini CLI adapters plus adapter mocks (`FR-AGENT-002/003/004`).
- Existing AGTX worktrees/tmux with policy-bounded host execution and Docker when available
  (`FR-RUN-001/002`).
- Configured deterministic gates, structured review, bounded repair, Git push, and one GitHub draft
  PR adapter (`FR-GATE-*`, `FR-REVIEW-001`, `FR-REPAIR-001`, `FR-GIT-*`).
- Per-run/project concurrency, time, attempt, disk, and estimated cost budgets (`FR-COST-*`).
- Human approval bound to current PR head (`FR-REL-001`).

## Deferred increments

- Staging adapters and rollback evidence follow the PR-ready MVP once runner and secret boundaries
  pass security testing (`FR-DEPLOY-001`).
- Scheduled maintenance follows durable recovery and budget enforcement (`FR-MON-001`).
- OpenHands/ACP, additional Git forges, trackers, and deployment providers are optional adapters.
- Organization accounts, RBAC, hosted persistence, remote runners, metering, and billing remain
  future architecture (`FR-TENANT-001`).

## Excluded

Production auto-deploy or auto-merge, arbitrary user-provided shell with blanket approval, browser
credential extraction, provider quota circumvention, Kubernetes, PostgreSQL, Temporal, a replacement
frontend, and importing whole third-party agent platforms.

## Reference hardware profile

- 4 logical CPU cores, 8 GiB RAM, 15 GiB free disk before builds, macOS or Linux.
- Default: one active agent, one validation process, 1 GiB scheduler/artifact soft disk budget per
  project, and output truncation with full logs stored under retention policy.
- Below 8 GiB available RAM, Docker isolation may be unavailable; host mode must show reduced
  isolation and retain the same command/path/secret policy.
- Disk preflight reserves the configured minimum plus estimated build allowance; dispatch pauses
  before the host reaches a critical threshold.

## MVP exit

All `AC-P1-*` through `AC-P5-*` criteria that apply to included scope pass on fixture repositories;
staging-specific `AC-P4-003` may be deferred to the next increment. Baseline upstream AGTX tests,
crash matrices, security abuse cases, backup/restore, and low-spec benchmarks must pass with recorded
evidence. Known exceptions require an accepted ADR and explicit release approval.
