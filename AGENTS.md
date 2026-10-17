# AGENTS.md

This file provides guidance to AI coding agents (Claude Code, Codex, Gemini, Copilot, OpenCode, Cursor, etc.) when working with the agtx codebase.

## What is agtx?

A terminal-native kanban board for managing multiple coding agent sessions in parallel. Each task gets its own git worktree, tmux window, and agent session. Written in Rust.

## Build & Test

```bash
cargo build --release
cargo test --features test-mocks
```

## Key Architecture

```
src/
├── main.rs        # CLI entry point, AppMode routing
├── lib.rs         # Module exports
├── tui/app.rs     # Main TUI app (largest file — board, popups, key handling)
├── db/schema.rs   # SQLite operations (tasks, projects, notifications)
├── db/models.rs   # Task, Project, TaskStatus structs
├── mcp/server.rs  # MCP server (JSON-RPC over stdio)
├── tmux/          # Tmux session/window management
├── git/           # Worktree creation, conflict detection
├── agent/         # Agent detection, spawn commands
├── config/        # GlobalConfig, ProjectConfig, WorkflowPlugin
└── skills.rs      # Skill deployment to agent-native paths
```

See [CLAUDE.md](CLAUDE.md) for the full architecture reference including common task patterns, code conventions, and testing approach.

## Core Concepts

- **Tasks** move through: `Backlog → Planning → Running → Review → Done`
- **Each task** gets a git worktree + tmux window + agent session
- **Plugins** (TOML) customize the task lifecycle per phase
- **MCP server** (`agtx mcp-serve`) exposes the board as tools over stdio
- **Global MCP** (`agtx mcp-serve`, no args) serves all projects via `~/.config/agtx/index.db`
- **Project MCP** (`agtx mcp-serve <path>`) serves a single project (used by the orchestrator)

## Sweep Skill

If you want to sweep this conversation into agtx tasks, use the sweep skill:

```
@skills/sweep/SKILL.md
```

Follow the Sweep workflow in that file.

## Rules

- Edit `src/` for behavior changes — never edit generated or compiled artifacts
- Run `cargo test --features test-mocks` before considering any change done
- Database migrations use `ALTER TABLE ... ADD COLUMN` (errors ignored if column exists)
- Mock-dependent tests use `#[cfg(feature = "test-mocks")]`
- See [CLAUDE.md](CLAUDE.md) for adding new fields, agents, plugins, shortcuts

## Engineering OS Extension Rules

The `feat/engineering-os` line extends agtx into a recoverable software-engineering workflow
controller. The upstream rules above remain authoritative for inherited code.

- Keep the existing task, plugin, worktree, tmux, database, and MCP abstractions unless an ADR
  records why they cannot satisfy a requirement.
- Keep execution local-first. Any hosted control plane must use the same explicit runner contract.
- Treat agent output as untrusted. Deterministic checks and policy gates decide whether work may
  advance; an agent must not approve its own unrestricted command execution.
- Never scrape browser sessions or provider tokens, bypass authentication, or evade provider
  quotas. Use supported CLI and API authentication flows.
- Require an explicit human release decision after a pull request is ready. Autonomous workflows
  may prepare a PR but may not merge or deploy it by default.
- Record executed validation commands and outcomes in
  `docs/engineering/IMPLEMENTATION_REPORTS.md`; do not claim checks passed without evidence.
- Pin external integrations by release and commit in `docs/architecture/REUSE_STRATEGY.md`, and
  update `THIRD_PARTY_NOTICES.md` when code or packaged assets are incorporated.
- Prefer allow-listed commands, isolated workspaces, bounded retries, leases, and resumable state.
  Unrestricted shells and blanket auto-approval are outside the product safety boundary.
