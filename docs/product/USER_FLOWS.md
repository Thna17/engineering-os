# User Flows

## Idea to approved engineering plan

```mermaid
sequenceDiagram
    actor PO as Product Owner
    participant UI as TUI/Web
    participant Plan as Planning Service
    participant Codex as Codex Adapter
    participant Store as SQLite/Artifacts
    PO->>UI: Submit idea and constraints
    UI->>Plan: Create planning run
    Plan->>Codex: Guided discovery prompt (read-only project context)
    Codex-->>Plan: Questions and draft artifacts
    Plan->>Store: Version idea, PRD, blueprint, ADR proposals, backlog
    Plan-->>UI: Show assumptions, conflicts, acceptance criteria
    PO->>UI: Accept or request revision
    UI->>Store: Record decision and artifact versions
    Store-->>Plan: Enable dependency-ready implementation tasks
```

Planning never grants write execution beyond its artifact workspace. Acceptance records the exact
artifact versions; later edits create a new revision and impact analysis.

## Default idea-to-PR workflow

```mermaid
sequenceDiagram
    actor Human
    participant S as Durable Scheduler
    participant C as Codex
    participant G as Gemini CLI
    participant R as Safe Runner
    participant Git as Git/PR Adapter
    Human->>S: Approve plan and start run
    S->>C: Plan task within read/write artifact policy
    C-->>S: Plan artifact
    S->>G: Implement in isolated worktree
    G-->>S: Candidate commit and handoff
    S->>R: Execute deterministic gates at commit SHA
    R-->>S: Signed-by-runner evidence
    S->>C: Review diff plus evidence
    C-->>S: Structured findings
    alt findings selected and repair budget remains
        S->>G: Repair selected findings
        G-->>S: New candidate commit
        S->>R: Rerun invalidated gates
        R-->>S: Updated evidence
        S->>C: Re-review changed scope
    else clean or accepted findings
        S->>Git: Idempotently prepare draft PR
        Git-->>S: PR/head/check state
        S-->>Human: Request release approval for exact head
    end
```

## Run-state model

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> AwaitingPlanApproval
    AwaitingPlanApproval --> Ready: approved
    AwaitingPlanApproval --> Draft: revise
    Ready --> Planning
    Planning --> Implementing
    Implementing --> Validating
    Validating --> Reviewing: gates pass
    Validating --> Repairing: gate failure is repairable
    Reviewing --> Repairing: selected findings
    Repairing --> Validating: candidate changed
    Validating --> Escalated: budget/cycle exhausted
    Reviewing --> PRPreparing: findings resolved/accepted
    PRPreparing --> AwaitingReleaseApproval
    AwaitingReleaseApproval --> Staging: approval + staging configured
    AwaitingReleaseApproval --> ReleaseReady: approval + no staging
    Staging --> ReleaseReady: health gates pass
    Staging --> Escalated: deployment/rollback failure
    ReleaseReady --> Completed: human release action recorded
    Ready --> Cancelled
    Planning --> Paused
    Implementing --> Paused
    Validating --> Paused
    Reviewing --> Paused
    Paused --> Ready: resume after reconciliation
```

An attempt may independently be `queued`, `leased`, `running`, `succeeded`, `failed`, `timed_out`,
`cancelled`, `unknown`, or `reconciling`. Run state advances only from persisted events and policy.

## Crash recovery

1. Acquire a time-bounded lease and persist the intended effect plus idempotency key.
2. Execute the effect and persist result/evidence transactionally with an outbox event.
3. On restart, mark expired running attempts `unknown`, inspect local process/worktree and provider
   state, then classify them as completed, safe-to-retry, failed, or operator-required.
4. Never retry PR creation, deployment, comments, or release actions before external reconciliation.
5. Present recovery decisions and evidence in the activity timeline.

## Human release approval

The UI shows PR, head SHA, gates, accepted/unresolved findings, staging health, cost, and risk. An
authorized human approves or rejects. Approval stores that evidence-set digest. Any head, gate,
finding disposition, environment, or policy change invalidates the approval and returns the run to
the appropriate state.

## Maintenance flow

An opt-in schedule emits a normal task proposal. Policy checks quota and maintenance window, then
runs read-only discovery. Changes require the same implementation, validation, review, PR, and human
release path as interactive work. Unchanged checks remain quiet; failures and actionable updates are
visible on the dashboard.
