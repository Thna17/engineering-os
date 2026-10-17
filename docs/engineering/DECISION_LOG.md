# Decision Log

| Date | Decision | Reason |
|---|---|---|
| 2026-10-17 | Import AGTX at `8ae68682f10b05ad77a8d1b307569946d871f814` with full history | It already provides the strongest reusable local orchestration substrate |
| 2026-10-17 | Rename clone remote to `upstream` and develop on `feat/engineering-os` | Preserve provenance and keep product work isolated |
| 2026-10-17 | Correct Cargo license metadata to Apache-2.0 | Match the preserved license file and upstream README; avoid publishing false MIT metadata |
| 2026-10-17 | Keep OpenSpec and Spec Kit as direct external CLI integrations | Existing AGTX plugins cover their artifact workflows without vendoring |
| 2026-10-17 | Use Ralph Review and Symphony as references | Their review/recovery patterns fit, while their runtimes overlap AGTX |
| 2026-10-17 | Treat OpenHands as an optional external runner/backend | Its sandbox/API can add value without importing its control plane |
| 2026-10-17 | Make the workflow engine durable and independent of the TUI | Long-running recovery cannot depend on an interactive polling process |
| 2026-10-17 | Require deterministic evidence and human release approval | Agents may propose and repair; trusted gates decide progression and release |
| 2026-10-17 | Keep SQLite as the MVP source of truth with durable repositories/events/outbox | Local single-writer requirements do not justify a new database |
| 2026-10-17 | Extend AGTX TUI, MCP, and web through one application service | Prevent duplicated transition logic and a premature frontend rewrite |
| 2026-10-17 | Store explicit provenance-based project memory | Avoid treating opaque chat history or model summaries as authoritative facts |
| 2026-10-17 | Store large artifacts content-addressed on local disk | Preserve SQLite responsiveness and evidence integrity without object storage |
| 2026-10-17 | Treat provider subscriptions/authentication as external operator capabilities | Respect supported authentication, quotas, licensing, and terms boundaries |
| 2026-10-17 | Defer production deployment and multi-user infrastructure | First prove local recovery, policy, evidence, and human release control |
