# Engineering Quality Standards

## Definition of done

A change is done only when linked requirements and acceptance IDs are present, design/ADR impact is
resolved, code follows component boundaries, tests appropriate to risk pass, evidence is recorded,
security and license implications are handled, documentation/migrations are updated, and no required
gate is failed, missing, stale, or falsely waived.

## Required gate classes

| Gate | Default trigger | Required evidence |
|---|---|---|
| Formatting | Source/config/docs change | Tool/version, command digest, current SHA, exit result |
| Static analysis | Source or dependency change | Compiler/linter output and configuration digest |
| Build | Source/manifest/toolchain change | Target/profile/features and artifact result |
| Unit | Source change | Test binary/suite counts and failures |
| Integration | Boundary/schema/provider change | Isolated fixture services and normalized results |
| Recovery | Scheduler/persistence/effect change | Injected crash points and post-restart invariants |
| Security | Runner/auth/policy/secret/network change | Abuse-case suite and threat-model link |
| License/supply chain | Dependency/distribution change | Lockfile, SBOM/license report, notice review |
| Documentation trace | Requirement/architecture/backlog change | Unique IDs and valid cross-links |
| Review | Any PR-ready change | Structured findings inventory and dispositions |

Current upstream Rust baseline is `cargo build`, `cargo build --features serve`, and `cargo test
--features "serve test-mocks"`, plus `node --test tests/web_client_tests.mjs`. Formatting and Clippy
are promoted to required only after existing upstream warning/debt is baselined so new work does not
hide behind legacy failures.

## Code and architecture standards

- Domain modules are deterministic and contain no process, filesystem, clock, random, database, or
  network calls; these enter through explicit ports.
- No UI or adapter writes workflow tables directly. Application commands enforce expected version,
  actor, policy, and idempotency.
- External commands use executable plus argument arrays. Shell evaluation requires a fixed reviewed
  script artifact and explicit policy.
- Errors preserve stable class/code and source chain while user-visible output is redacted.
- Database mutations use short transactions, foreign-key/invariant checks, and versioned migrations.
- Async/background work has cancellation, timeout, ownership, and shutdown behavior.
- Logs include correlation/run/step/attempt identifiers and exclude source content or credentials by
  default.

## Review standards

Codex review covers correctness, requirement fit, security, reliability/recovery, concurrency,
backward compatibility, data migration, observability, tests, licensing, and operability. Findings
need concrete evidence and tight locations; style preferences without a documented standard are not
blocking. The implementing agent cannot be the sole accepting reviewer for high-risk changes.

P0 means active critical security/data-loss or release blocker; P1 likely correctness/security or
recovery failure; P2 material maintainability/operability issue; P3 minor improvement. P0/P1 require
resolution or explicit authorized risk acceptance. Repair runs receive selected findings only and
must not rewrite unrelated scope.

## Change risk levels

- **Low:** docs/UI copy or isolated reversible behavior; focused tests.
- **Medium:** ordinary domain/adapter behavior; unit plus boundary integration tests.
- **High:** migrations, runner/policy/auth/secrets, destructive Git, scheduler recovery, approvals,
  deployment; design review, abuse tests, recovery tests, rollback evidence, and human approval.

## Reproducibility and dependencies

Commit lockfiles; pin toolchains and container image digests; use `--locked`/equivalent in release
gates; record external CLI versions; isolate caches from source; fail clearly when offline inputs are
missing. Dependency upgrades are dedicated changes with changelog/security/license review. No
runtime “latest” downloads in deterministic gates.

## Documentation traceability

Functional IDs use `FR-<AREA>-NNN`, non-functional `NFR-<AREA>-NNN`, acceptance `AC-Pn-NNN`, stories
`US-<AREA>-NNN`, backlog `EOS-NNNN`, and ADR `ADR-NNN`. New requirements must link at least one
acceptance criterion and backlog task before implementation. Deleted IDs remain reserved with a
supersession note.
