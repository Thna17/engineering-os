# Engineering OS Master Roadmap

Requirement and acceptance IDs are defined in `docs/product/PRD.md`; implementable work and hard
dependencies are defined in `TASK_BACKLOG.md`.

## M0 / Phase 0 — Baseline and blueprint (current)

Import AGTX history, audit reusable components, align license metadata, define architecture and
security boundaries, run upstream validation, and establish evidence-based progress records.

Exit: `EOS-0001..0003`; governance and blueprint files exist; upstream SHA and external pins are
recorded; baseline commands have real outcomes; `AC-P1-001/002` are specified and traceable. Full
Rust validation remains required before M0 closes.

## M1 / Phase 1 — Durable workflow kernel

- Add schema-versioned migrations and tables for workflow runs, steps, attempts, leases, events,
  artifacts, validation evidence, findings, approvals, and idempotency keys.
- Extract transition decisions from the TUI into a deterministic application service/reducer.
- Add scheduler ownership, heartbeats, bounded exponential retry, cancellation, and startup
  reconciliation.
- Model the default Product Owner → Codex plan → Gemini implement → tests → Codex review → Gemini
  repair → PR-ready → human approval workflow as versioned configuration.

Exit: `EOS-0101..0108`; `AC-P2-001/002`, `NFR-REL-001/002/003`. Kill/restart tests demonstrate no
lost task, duplicate observable side effect, or false success.

## M2–M3 / Phase 2 — Planning, memory, safe runners, agents, and budgets

- Implement versioned planning/blueprint/ADR/memory artifacts (`EOS-0201..0206`).
- Define runner request/result contracts and local-host plus Docker implementations.
- Enforce executable/argument policies, workspace containment, environment redaction, timeouts,
  output limits, resource budgets, and optional network denial.
- Capture reproducible test evidence at an immutable commit SHA.
- Add provider authentication and quota diagnostics without accessing raw tokens.

Exit: `EOS-0201..0308`; `AC-P1-001/002`, `AC-P2-003`, `AC-P5-002/003`. A fixture project completes
planning and implementation inside a bounded runner; disallowed commands and path escapes are
rejected and audited.

## M4–M5 / Phase 3 — Quality, review, repair, PR, dashboard, and approval

- Add structured findings with stable IDs, priorities, locations, fingerprints, and disposition.
- Run review and repair in separate attempts/worktrees with bounded cycles and regression tests.
- Add GitHub provider interface for draft PR creation/update and check/status ingestion.
- Add head-bound human release approval and invalidate it on new commits.

Exit: `EOS-0401..0507`; `AC-P3-001/002/003`, `AC-P4-001/002`, `AC-P5-001/003`. The reference workflow
prepares one reviewable draft PR with complete evidence and stops at the human gate.

## M6 / Phase 4 — Staging, maintenance, and ecosystem

- Surface workflow timelines, evidence, findings, retry controls, and approvals in TUI/web.
- Harden OpenSpec and Spec Kit adapters with version/capability checks.
- Add optional OpenHands/ACP runner exploration behind a feature flag.
- Publish migration, backup/restore, incident, and integration authoring documentation.

Exit: `EOS-0601..0604`; `AC-P4-003`. Local operators can understand and recover every state without
inspecting SQLite manually, deploy to one staging adapter, and verify rollback evidence.

## M7 / Phase 5 — Commercial multi-user discovery and control plane

- Introduce authenticated API, organizations, users, project membership, RBAC, audit export, and
  tenant-scoped secrets.
- Implement PostgreSQL and object-storage repositories plus enrolled remote runners using short-lived
  credentials and mutual authentication.
- Add quotas, billing/metering boundaries, retention, backups, regional deployment, and SLOs.

Implementation begins only after discovery quantifies tenancy, concurrency, availability, regional,
runner, retention, and cost needs and accepts new ADRs. PostgreSQL, object storage, and a distributed
dispatch system are candidates, not defaults.

Exit: `AC-P7-001/002`. `FR-TENANT-001` is refined into accepted requirements; threat-model review,
tenant-isolation tests, restore drills, and staged production-readiness review pass. This phase does
not weaken the local-only distribution.
