# Implementation Reports

## IR-0001 — Upstream import and baseline audit (2026-10-17)

Scope: environment inspection, non-destructive import, architecture/reuse research, governance,
license metadata correction, and baseline validation.

### Source evidence

- Upstream commit: `8ae68682f10b05ad77a8d1b307569946d871f814`
- Upstream authored date: `2026-10-02T05:36:16-07:00`
- Branch: `feat/engineering-os`
- License file: Apache License 2.0, preserved unchanged

### Validation evidence

Results are appended after executing the baseline commands. A result is recorded as passed only
when the command actually exits successfully in this environment. Environment/preflight failures
are recorded separately from test failures.

| Command | Result | Evidence |
|---|---|---|
| `node --test tests/web_client_tests.mjs` | Passed | 3 tests passed, 0 failed, Node 22.22.2, duration 89.278 ms |
| `cargo build --verbose` | Passed in container | Rust 1.90.0 Bookworm container; finished dev profile in 2m25s; 10 upstream warnings |
| `cargo build --verbose --features serve` | Infrastructure failure | Compilation reached `agtx`; rustc failed with `No space left on device` (exit 101) |
| `cargo test --verbose --features "serve test-mocks"` | Not run | Docker Desktop became unable to start after disk exhaustion; native Cargo is absent |

Container image: `rust:1.90.0-bookworm`, digest
`sha256:3914072ca0c3b8aad871db9169a651ccfce30cf58303e5d6f2db16d1d8a7e58f`.
The 1.4 GB `target-container` directory created by validation was deleted after the failure, raising
available data-volume space from roughly 136 MB to 5.8 GB. A bounded Docker Desktop restart attempt
still returned `Docker Desktop is unable to start`. No source files were removed.

Rerun after recovering Docker or installing a native pinned Rust toolchain:

```bash
cargo build --verbose
cargo build --verbose --features serve
cargo test --verbose --features "serve test-mocks"
node --test tests/web_client_tests.mjs
```

### Material changes

Only governance/documentation, `AGENTS.md`, and the manifest license identifier were changed. No
AGTX runtime behavior or third-party source was imported.

## IR-0002 — Commercial engineering blueprint (2026-10-17)

Scope: product requirements, personas, stories, flows, MVP boundary, system/component/data and
deployment architecture, integration contracts, dependency backlog, quality/test strategy, security
requirements, and governance updates.

Evidence:

- Product requirements use stable `FR-*` and `NFR-*` IDs with measurable `AC-P*` criteria.
- `TASK_BACKLOG.md` maps `EOS-*` work to hard dependencies, evidence, and acceptance.
- ADR-009 through ADR-014 make persistence, UI, memory, artifacts, subscription, and deployment
  choices traceable.
- Mermaid source defines product, workflow, state, system, data, milestone, and deployment views.
- No runtime feature or third-party code was added in this phase.

| Validation | Result | Evidence |
|---|---|---|
| `git diff --check` | Passed | No patch whitespace errors |
| Required-document inventory | Passed | All 14 requested blueprint files exist and are non-empty |
| Requirement-ID definition audit | Passed | 30 functional, 13 non-functional, 16 product acceptance, 43 backlog, and 37 security definitions; no duplicate definition IDs |
| Markdown fence audit | Passed | All blueprint Markdown files have balanced code fences |
| Local Markdown link audit | Passed | Every relative link in product, architecture, engineering, and security Markdown resolves |

## IR-0003 — Gemini implementation prompt playbook (2026-11-19)

Scope: convert the dependency-aware backlog into copy-paste Gemini CLI prompts with a persistent
bootstrap contract, task-level scope, required tests/evidence, security boundaries, stop conditions,
Codex review, and selected-finding repair.

The playbook covers baseline recovery, EOS-0101 through EOS-0507, MVP readiness, and optional
EOS-0601 through EOS-0604 increments. It deliberately requires one reviewed prompt at a time and
does not authorize commits, pushes, merges, production deployment, unsafe shell execution, browser
credential access, or quota bypass.

Validation:

| Command/check | Result | Evidence |
|---|---|---|
| `git diff --check` | Passed | No patch whitespace errors |
| Prompt inventory | Passed | Prompts 0–29, two optional increments, Codex review, and Gemini repair are present |
| Markdown fence audit | Passed | 68 code fences; balanced |
| Rust/runtime tests | Not run | Documentation-only change; existing native Rust/Docker blocker remains recorded in IR-0001 |
