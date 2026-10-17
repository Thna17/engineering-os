# Architecture Decisions

This file is the initial ADR index. New decisions receive a dated `ADR-NNN` entry with status,
context, decision, consequences, and superseding links.

## ADR-001: Fork AGTX as the product foundation

**Status:** Accepted, 2026-10-17.

AGTX already implements the local multi-project board, worktrees, tmux sessions, agent adapters,
plugins, MCP, SQLite, and operator UI. Engineering OS will extend these abstractions rather than
replace them. This preserves working code and the Apache-2.0 history while concentrating effort on
recovery, policy, validation, review, and release gates.

## ADR-002: Local-first modular monolith

**Status:** Accepted.

The first commercial-quality release remains one Rust application with explicit internal ports:
repositories, workflow engine, runners, providers, artifacts, and policy. SQLite and local files are
the default. Interfaces must permit PostgreSQL/object storage and remote runners later, but no
network service is required for a single-user installation.

## ADR-003: Deterministic workflow reducer plus durable effects

**Status:** Accepted.

Workflow state transitions are pure decisions over persisted state and typed events. Side effects
(agent launch, test execution, Git push, PR creation) are attempts with idempotency keys, leases,
timestamps, and captured results. On restart, the scheduler reconciles claimed attempts and external
state before retrying. The TUI observes and requests transitions; it does not own the scheduler.

## ADR-004: Policy-bounded execution

**Status:** Accepted.

Commands originate from versioned workflow definitions and resolve to structured executable/args,
working directory, environment allow-list, timeout, network profile, and resource budget. Project
owners may approve policy changes, but agents cannot grant themselves broader execution. Local host,
container, and future remote runners implement the same contract.

## ADR-005: Agent roles are configurable adapters

**Status:** Accepted.

Codex planning/supervision and review plus Gemini implementation/repair are the default workflow,
not hard-coded domain logic. Agent adapters declare capabilities, authentication status, supported
resume semantics, and structured-output support. Other agents can fill a role when policy allows.

## ADR-006: Evidence gates, not agent assertions

**Status:** Accepted.

Only the deterministic runner may mark a validation gate passed. Evidence includes commit SHA,
command specification, tool versions, start/end time, exit status, and retained logs. Review
findings use stable IDs and repairs reference the exact finding and attempt.

## ADR-007: Human release approval is mandatory by default

**Status:** Accepted.

Automation may create or update a pull request and collect passing checks. Merge and deployment
require a recorded human decision bound to repository, PR, head SHA, approver identity, and time.
Changing the head invalidates approval. Future policy may allow narrow exceptions through a new ADR.

## ADR-008: External spec and sandbox tools remain adapters

**Status:** Accepted.

OpenSpec and Spec Kit integrate through pinned CLIs and artifact contracts. OpenHands may be an
optional execution backend. Ralph Review and Symphony inform internal design. No whole upstream
codebase is imported without a separate ADR and notice update.

## ADR-009: SQLite remains the authoritative local database

**Status:** Accepted.

The MVP has a single local scheduler writer, existing rusqlite/WAL infrastructure, and no cross-host
transaction requirement. Engineering OS adds numbered migrations, repositories, leases,
events/outbox, backup, and integrity checks to SQLite. PostgreSQL requires measured multi-user
concurrency, availability, or operational needs and a superseding ADR.

## ADR-010: Extend the existing TUI and web application

**Status:** Accepted.

TUI, MCP, and Axum/PWA call one application service and receive the same query models. The existing
frontend gains timelines, evidence, findings, budgets, and approvals. A separate stack would
duplicate authorization and state behavior without an MVP requirement.

## ADR-011: Explicit project memory with provenance

**Status:** Accepted.

Persistent memory consists of versioned facts, constraints, accepted decision summaries, patterns,
and operational notes with source, confidence, actor, and supersession. Chat transcripts or model
summaries do not silently become truth. SQLite FTS is default; embeddings remain optional.

## ADR-012: Content-addressed local artifact storage

**Status:** Accepted.

Large logs and artifacts are stored outside SQLite by SHA-256 digest, with transactional metadata,
atomic writes, retention, and backup manifests. This keeps the database responsive and detects
tampering or missing evidence without requiring object storage.

## ADR-013: Provider subscriptions are operator-owned external capabilities

**Status:** Accepted.

Engineering OS uses documented CLIs/APIs and supported authentication, stores opaque profile
references, respects quotas, and does not scrape browser tokens or imply consumer-plan automation
rights. Provider entitlement and terms remain the operator's responsibility.

## ADR-014: Staging uses adapters; production remains human-controlled

**Status:** Accepted.

Staging providers implement a versioned deploy/status/health/rollback contract with scoped secrets
and evidence. The MVP exposes no autonomous production merge or deployment capability.
