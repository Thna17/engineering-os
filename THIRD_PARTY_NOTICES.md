# Third-Party Notices

Engineering OS is derived from AGTX by Fynn Fluegge and contributors. The repository history and
the upstream Apache License 2.0 file are preserved. Upstream source:
`https://github.com/fynnfluegge/agtx`, imported at
`8ae68682f10b05ad77a8d1b307569946d871f814`.

The Rust dependency inventory and exact resolved versions are recorded in `Cargo.lock`; each
dependency remains subject to its own license. Distribution builds must generate and review a
machine-readable dependency/license report before release.

The following projects were reviewed as potential integrations or architectural references. No
source or assets from them were copied by this change:

| Project | License | Use |
|---|---|---|
| Fission-AI/OpenSpec | MIT | Optional external CLI integrated through existing workflow plugin |
| github/spec-kit | MIT | Optional external CLI integrated through existing workflow plugin |
| kenryu42/ralph-review | MIT | Architectural reference only |
| openai/symphony | Apache-2.0; upstream includes NOTICE | Architectural reference only |
| OpenHands/OpenHands | MIT | Potential optional external runner/backend |

If any third-party code, templates, or packaged assets are added later, this notice must include the
exact version, copyright/license text obligations, modifications, and distribution location.
