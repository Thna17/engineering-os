# Reuse Strategy

Review date: 2026-10-17. Commit pins are immutable inspection points; releases are recorded for
operator-facing dependency policy. No third-party source was copied into this repository.

| Project | Inspected release / commit | License | Classification | Decision |
|---|---|---|---|---|
| Fission-AI/OpenSpec | `v1.14.3`, `c794978f4f8845ce59693373c9dd882ee776588c` | MIT | DIRECT INTEGRATION | Keep AGTX's OpenSpec plugin and invoke a separately installed, pinned CLI. Consume Markdown specs/tasks as artifacts. Do not embed its source. |
| github/spec-kit | `v1.2.1` (`9dca9bf42a031765ee5e5f2371137f2f26967e11`); main inspected at `c6a4c18f1d01a8bec241c618eb6dfea952508744` | MIT | DIRECT INTEGRATION | Keep the existing plugin adapter. Pin the `specify-cli` distribution in a project tool manifest when enabled. Treat templates and extensions as external inputs. |
| kenryu42/ralph-review | `v0.2.6`, `1f9b8b96fbb339cdd3fde9e89d9bba5b3f9e1a5f` | MIT | ARCHITECTURAL REFERENCE | Reuse the ideas of disposable review worktrees, stable finding IDs, deduplication, explicit selection, separate fixer runs, bounded iterations, and inspectable handoff. Implement against AGTX primitives instead of adding a second tmux/worktree controller. |
| openai/symphony | main/nightly `be10a1b79df723d6d7612b5651c8522704dafb2e`; latest release `v0.0.3` at `1c0fb6c8e8ef9031a2c861e62af5f9e66cee39cb` | Apache-2.0 | ARCHITECTURAL REFERENCE | Adopt its separation of policy, configuration, coordination, execution, integration, and observability; deterministic workspace identity; bounded concurrency; reconciliation; exponential retry; and workflow-defined human handoff. Do not import the Elixir implementation. |
| OpenHands/OpenHands | `v1.27.1` (`f07aebdf5cdd71f4a05ebcf30f8bef5e4e3b70b9`); main inspected at `045290739f0af2ae4cc89d2a209c6bd930bb81ee` | MIT | EXTERNAL TOOL | Offer a future optional runner/backend integration through documented APIs or ACP. Its Docker isolation and remote backend model are useful, but Agent Canvas, automation, and hosted control-plane code remain separate. |

## Integration rules

1. Each enabled external CLI has an exact version, integrity hash where available, capability
   probe, and supported install instructions.
2. Integrations exchange versioned files or structured protocol messages. They do not patch vendor
   internals or depend on undocumented browser state.
3. External tools run with the minimum workspace, environment, network, and secret access required.
4. Telemetry defaults and opt-out controls must be surfaced. OpenSpec, for example, documents
   anonymous telemetry and `DO_NOT_TRACK`/`OPENSPEC_TELEMETRY=0` controls.
5. Incorporating any source or packaged asset requires an ADR, license compatibility review,
   attribution update, and maintenance owner.

## Why not vendor entire projects

AGTX already owns the overlapping workflow, worktree, tmux, agent, and UI responsibilities.
Vendoring would create competing state machines and duplicate security boundaries. Direct adapters
preserve upstream upgrade paths, while architectural reuse captures proven behavior without
creating a mixed-language monolith.
