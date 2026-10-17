# Test Strategy

## Principles

Test deterministic logic below process boundaries, contracts at every adapter, and only a small
number of complete workflows. Use real Git/SQLite/filesystem behavior in temporary directories;
mock provider networks and agent CLIs by default. Live provider tests are opt-in, budgeted, and never
required for ordinary contributor CI.

## Test layers

| Layer | Scope | Examples |
|---|---|---|
| Domain unit/property | Pure reducer, DAG, budgets, approval validity, finding fingerprints | Every state/event pair; random DAGs; version conflicts |
| Repository | SQLite schema, transactions, leases, migration, outbox | Crash transaction, concurrent claims, backup/restore |
| Contract | Runner, agent, forge, deploy, plugin, events | Golden request/results, previous schema minor, malformed/redacted data |
| Component | Scheduler+repository, quality+runner, review+repair | Fake clock/process/provider and deterministic fixtures |
| Integration | Real Git worktrees/tmux/host runner/Docker where supported | Dirty tree, conflicts, signals, timeout, resource exhaustion |
| UI/API | Application service through TUI/web/MCP adapters | Authorization, optimistic concurrency, consistent state rendering |
| End-to-end | Fixture idea to draft PR against fake forge | Default happy path and bounded failure paths |
| Operational | Packaging, upgrade, recovery, backup, low-spec | Kill matrix, schema upgrade, restore drill, benchmark |

## Determinism

Inject clocks, IDs, random jitter, process executors, provider clients, and resource probes. Tests
must not rely on wall sleeps where a fake clock/event can advance. Each fixture declares repository
SHA, workflow/policy digest, tool versions, expected events, and final state. Flaky tests are defects;
quarantine requires owner, reason, expiry, and issue.

## Required scenario matrices

### Recovery matrix

Kill before claim, after lease, before effect, during effect, after external success/before local
commit, after local commit/before outbox delivery, and during shutdown. Assert one valid terminal or
operator-required state, no duplicate observable external effect, and preserved evidence. Covers
`AC-P2-002`, `NFR-REL-001/002/003`.

### Permission abuse matrix

Traversal, symlink escape, primary-worktree cleanup, shell metacharacters, environment expansion,
undeclared executable, secret request, network request, forked child after timeout, oversized output,
malicious repository instruction, and plugin permission escalation. Assert denial, cleanup, redacted
audit, and unchanged policy. Covers `AC-P2-003`, `NFR-SEC-*`.

### Evidence and approval matrix

Changed SHA, command, toolchain, policy, finding disposition, staging revision, approver role, expiry,
and replayed idempotency key. Assert stale gates/approvals invalidate and cannot release. Covers
`AC-P3-002`, `AC-P4-001/002`.

### Review/repair matrix

Duplicate/reworded finding, repaired location shift, unresolved finding, test regression, agent loss,
rate limit, max cycle, max cost, and accepted risk. Assert stable inventory, bounded cycles, retest,
and escalation. Covers `AC-P3-001/003`.

## Requirement-to-test coverage

Every `FR-*` has at least one automated test or an explicit design-only marker. Every `AC-*` has an
executable scenario in `tests/acceptance/` by its ID. A generated trace report maps requirement →
acceptance → test → last evidence. `EOS-0004` enforces unique IDs and links in CI.

## Provider testing

Adapter contract suites run against fake executables that emit normal, malformed, rate-limited,
authentication-required, partial, and oversized output. Opt-in smoke tests run only when a human has
configured supported authentication and a cost ceiling; they check protocol compatibility, not model
quality. No tests inspect tokens or browser state.

## Performance and resource tests

On the reference 4-core/8-GB profile measure idle memory/CPU, 100 active task records, scheduler
recovery latency, query pagination, artifact/log growth, disk preflight, and one-agent/one-gate
concurrency. Enforce `NFR-PERF-001/002`; publish environment and variance. Do not run multiple model
agents merely to claim concurrency on insufficient hardware.

## Release test order

Fast domain/repository tests run first, then upstream tests, contract/component tests, security and
crash matrices, UI tests, E2E fake-provider flow, packaging/license checks, backup/restore, and
low-spec benchmark. A stage may stop on failure, but all not-run tests remain visibly not run.
