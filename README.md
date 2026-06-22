# icc-demo

Proof-of-Concept demonstration for the ICC Code Connect license. This repo holds the demo that closes the ICC PoC and moves us toward the 12-month SaaS agreement.

This is a demonstration of content handling and surfacing, not a technical review and not a written report. It runs on the LIVE Hauska spine and MCP gate (no mock data). Visual design is deliberately minimal: the demo is about UX and the content path, not polish. The operator will show samples of the real surfaces that consume the plan-review.

Start here:
- [`docs/00_scope.md`](docs/00_scope.md) — the scoped brief: decisions locked, the three ICC acceptance criteria, hard PoC constraints, and the deliverables to produce.
- [`docs/01_architecture_scaffold.md`](docs/01_architecture_scaffold.md) — how the demo wires to the live spine and MCP, the reusable plan-review formalization, the citation path, usage tracking, and the isolated demo instance.

Two titles only: 2018 IBC and 2018 IPMC, via the Code Connect API. Plan entirely around these.

Portfolio record and decisions: `doc_repo/_dispatches/2026-06-22_icc_poc_demo_scope.md`.
