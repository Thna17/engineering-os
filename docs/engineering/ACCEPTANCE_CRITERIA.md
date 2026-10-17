# Acceptance Criteria

## Product workflow

- A Product Owner can create work in one of multiple projects and select a versioned workflow.
- Codex can produce a persisted plan; Gemini can implement it in an isolated worktree; configured
  tests run deterministically; Codex review produces structured findings; Gemini repair addresses
  selected findings; automation prepares a PR; the workflow stops for human release approval.
- Roles are adapter-configurable and do not require Codex or Gemini in core state transitions.

## Reliability

- Every side effect has an attempt record and idempotency key.
- A process crash at each workflow boundary recovers without losing state or duplicating a PR,
  commit, or external comment.
- Leases expire safely; retries are bounded and use jittered exponential backoff; terminal failure
  is visible and actionable.
- Backup and restore preserve projects, workflow state, approvals, and evidence.

## Security

- Agent-supplied text cannot expand command, filesystem, network, or secret permissions.
- Workspaces cannot escape approved roots through traversal or symlink behavior.
- Raw credentials are never logged, stored in prompts, returned through MCP, or exposed to child
  processes that do not need them.
- No browser-token scraping, quota bypass, blanket auto-approval, or unrestricted default shell.
- PR merge/deploy requires human approval bound to the current head SHA.

## Validation and review

- A passed gate contains actual exit-zero evidence from the configured deterministic command.
- Tool versions and dependency locks are recorded. Offline/reproducible modes fail clearly when an
  unpinned dependency is required.
- Findings have stable IDs and disposition; repair is bounded; a changed patch is retested.
- Test logs distinguish pass, fail, timeout, infrastructure error, cancellation, and not-run.

## Local and multi-user operation

- The full core workflow runs with local SQLite, local Git, and local agents without a SaaS account.
- Multi-user services authenticate every request, enforce tenant/project roles, and audit mutations.
- Remote runners receive short-lived, scoped work and cannot enumerate unrelated projects.

## Release gate

Release candidates require clean license/notice checks, supported-platform builds, unit/integration
tests, crash-recovery tests, threat-model review, migration/rollback notes, and a human sign-off.
