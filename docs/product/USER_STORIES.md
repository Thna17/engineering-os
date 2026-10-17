# User Stories

| ID | Persona | Story | Acceptance links |
|---|---|---|---|
| US-PLAN-001 | P-01 | As a Product Owner, I can answer guided questions and save an idea brief so later artifacts preserve my intent and assumptions. | AC-P1-001 |
| US-PLAN-002 | P-01 | I can accept or revise a generated PRD before implementation tasks become eligible. | AC-P1-001, AC-P2-001 |
| US-BLUE-001 | P-02 | As an Engineering Lead, I can regenerate affected blueprint sections and see which requirement or ADR caused each change. | AC-P1-001/002 |
| US-ADR-001 | P-02 | I can supersede an accepted ADR while preserving the original and identifying impacted components/tasks. | AC-P1-002 |
| US-BACK-001 | P-02 | I can inspect a DAG of implementation work and understand why a task is blocked. | AC-P2-001 |
| US-DELEG-001 | P-02 | I can assign an agent role and execution policy independently of a specific provider. | AC-P3-001 |
| US-CODEX-001 | P-02 | Codex can plan and review but cannot attest that deterministic tests passed or approve release. | AC-P3-001/002, AC-P4-002 |
| US-GEM-001 | P-03 | Gemini CLI can implement and repair in its worktree using supported authentication and a bounded permission envelope. | AC-P3-001/003 |
| US-PROV-001 | P-04 | I can run provider diagnostics that report installed/authenticated/rate-limited states without revealing tokens. | NFR-SEC-003 |
| US-RUN-001 | P-04 | I can choose host or Docker isolation and preview filesystem, network, secret, and resource grants before execution. | AC-P2-003 |
| US-GATE-001 | P-03 | I can reproduce a failed gate from its exact command, commit, tool version, and log. | AC-P3-002 |
| US-REV-001 | P-03 | I can triage stable review findings and select which findings enter repair. | AC-P3-003 |
| US-REPAIR-001 | P-02 | I can cap repair cycles and receive an escalation when the cap or budget is reached. | AC-P3-003 |
| US-PR-001 | P-01 | I receive one draft PR containing requirement, test, review, risk, and handoff evidence. | AC-P4-001 |
| US-STAGE-001 | P-04 | I can deploy the approved revision to staging through a declared adapter and inspect health/rollback evidence. | AC-P4-003 |
| US-REL-001 | P-01 | I can approve or reject the current PR head, and my approval cannot silently apply to later commits. | AC-P4-002 |
| US-DASH-001 | P-02 | I can see all projects ordered by required action, failure, cost pressure, and recent progress. | AC-P5-001 |
| US-MEM-001 | P-03 | I can find a decision or verified project fact with source, timestamp, and supersession status. | AC-P5-003 |
| US-REC-001 | P-04 | After restart I can see which attempts resumed, reconciled, retried, or require intervention. | AC-P2-002 |
| US-COST-001 | P-04 | I can set concurrency/time/cost/disk limits and see a safe pause before exceeding them. | AC-P5-002 |
| US-MON-001 | P-02 | I can opt a project into maintenance checks that create ordinary reviewable tasks, never silent production changes. | FR-MON-001 |
| US-PLUGIN-001 | P-02 | I can inspect a plugin’s schema, compatibility, required tools, and permissions before enabling it. | AC-P2-003 |
| US-TENANT-001 | P-05 | As an Administrator, I can restrict projects and runners by organization role with complete audit trails. | Future FR-TENANT-001 |
