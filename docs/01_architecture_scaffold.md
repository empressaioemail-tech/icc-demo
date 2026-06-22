# Architecture scaffold

The demo runs on the LIVE Hauska spine and MCP gate. No mock data. Minimal visual design — the demo is the UX and the content path. Build by assembling what exists; the only net-new construction is the formalized, reusable plan-review and the demo surfaces' thin UX.

## What already exists (assemble, don't rebuild)

- IccCodeConnect adapter in hauska-engine — the consumption side (credential-gated). Wire it to the real Code Connect API and ingest IBC + IPMC.
- The MCP gate meters every call; F1 atom-grain attribution populates request_log.atom_ids_returned — real usage tracking.
- The read-contract carries provenance and citation lineage; atom_trace exposes it — the basis for the formal reference.
- An existing plan-review shape (cortex-api finding-engine, the L-surface, the codex_finding_generation MCP tool) — the starting point to formalize into a reusable module.

## Data path (consumption, isolated)

ICC Code Connect API → IccCodeConnect adapter (hauska-engine) → IBC/IPMC code-section atoms, ingested into an ISOLATED demo partition/instance with a designated Administrator. The ICC-derived atoms carry ICC provenance. Keep our proprietary reasoning and calibration separate from these ICC-content-derived atoms (derivative-works clause). Tear-down of the instance is the wind-down.

## Gate path (surfaces consume via the live gate)

Surfaces call the live MCP gate (X-Hauska-Key; the public/codex/reporting/map product gates). They never call the engine directly. Citation lineage comes through atom_trace and the read-contract; usage is metered at the gate. This is the layer-in-between guarantee made structural: the surface gets reasoned output and a formal reference through the gate, never the raw code wholesale.

## Surfaces (UX-first, minimal design)

1. Brief extension (live): surface ICC content with a formal citation inside the existing brief flow. Reuse the extension's chrome; no new visual design.
2. Generalized plan-review (the reusable artifact): a thin UX shell over the formalized plan-review module (below), shown in two contexts — municipal (IPMC checks) and B2B/architect (IBC checks). Same module, two surface shells. The operator will show samples of the real surfaces that will consume it.

## The reusable plan-review module (formalize, build to last)

Construct the plan-review as a durable, surface-independent module, not a one-off demo. Formalize the existing shape into this contract:

- Inputs: a submission/context, the jurisdiction, and the applicable ICC titles + editions (IBC, IPMC).
- Process: retrieve the applicable code atoms via the gate → reason over them → produce findings, each with a formal ICC citation and a read-contract confidence object (asserted-with-provenance at PoC; calibrated later via arrow two).
- Outputs: findings + a formal reference section + per-query usage events.

Decoupled from any single surface so municipal and B2B both consume it unchanged. This module is reused beyond the demo; it should later reconcile into the homes as the plan-review function (the one Codex/plan-review surfaces and AEC-cortex consume through the gate). Treat the demo as the formalization milestone for it.

## Citation and formal reference (highest scrutiny)

From the read-contract citation lineage, render a formal reference section per answer: ICC section identifier + title + edition (e.g., 2018 IBC section number). Agent reasoning sits above; inline citation markers link to the reference; the raw code is never republished wholesale. Confirm the identifier format with ICC's technical team before the demo.

## Usage tracking and pay-per-query

The live gate meters every call; F1 atom-grain attribution gives the per-atom (per ICC section) DID list. Build a scoped content-usage view: ICC title/section, call counts, per-surface and per-query. Show the pay-per-query mechanism — meter → per-query price → would-charge — as a demonstrated flow, NOT a live transaction (PoC forbids commercial use). This view is scoped to content usage; it does not expose our calibration or atom internals.

## Isolation, security, wind-down

Separate demo instance; designated Administrator; access controls; nothing in prod depends on PoC access; tear-down equals wind-down. Build so removing ICC access removes nothing the production spine needs.
