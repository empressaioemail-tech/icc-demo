# Build plan (Gate-1-independent path)

Grounded in a read of the live source on 2026-06-22: `hauska-engine`, `hauska-mcp-server`, `legacy-design-tools` (holds the finding-engine and cortex-api), `hauska-atom-contract`, `hauska-brief-extension`. This plan supersedes the sequencing assumptions in [`02_execution_plan.md`](02_execution_plan.md) where ground truth differs, and routes all work that does not require consuming ICC content. The execution plan's designs (citation, module contract, demo script) stand; this plan says what to build, in what order, against what is actually on disk.

## What ground truth changed

The execution plan treated WS-A (ingestion) as the foundational blocker that gates nearly everything. It is not. The adapter, the isolation tenant, the provenance and citation lineage, the modular finding-engine, and the gate metering schema all already exist. The only thing that genuinely blocks on Gate 1 (ICC's access-mechanism answer plus credentials) is the live OAuth handshake, reconciling the adapter's assumed API contract against ICC's real spec, and the actual ingestion of real 2018 IBC and 2018 IPMC. Everything else, including a complete end-to-end dry run, builds now.

Three specifics worth holding.

The adapter is built and credential-gated. `packages/corpus/src/adapters/icc-code-connect/` in hauska-engine implements the full `CodeSourceAdapter` contract (discover, fetch, metadata, normalize) with an OAuth2 client-credentials client against `api.iccsafe.org`. It runs in three modes: live (credentials present), mock (fixtures), and unconfigured (inert). Its API contract carries nine `@assumption` tags because ICC has not yet provided the OpenAPI spec; Gate 1 resolves those. Fixtures exist for 2021 IRC, 2018 IRC, 2021 IBC, 2021 IECC, 2021 A117. 2018 IPMC is not in fixtures and 2018 IBC needs confirmation.

The layer-in-between is enforced at ingestion. `packages/corpus/src/model-code/extractor.ts` never hosts copyrighted verbatim text. It sets `verbatimTextDeepLink` to the ICC viewer and puts only the reasoning layer in `bodyText`. The atom for an ICC section is provenance plus our reasoning plus a deep link, not the section body. This is criterion 2 satisfied by construction, and it is the clean line for the derivative-works separation: ICC content stays a pointer, our reasoning is ours.

The isolation primitive exists. ICC content ingests under the `jurisdictionTenant` value `icc-model-code` (`ICC_MODEL_CODE_TENANT`), with the five-value `accessPolicy` from ADR-017 stamped at atomization. The isolated demo instance is this tenant scope plus access controls plus a designated Administrator, not a new system.

## The genuine net-new builds

Two pieces of real work carry the demo, and neither needs ICC content.

Formal reference section (criterion 1). The finding-engine at `legacy-design-tools/lib/finding-engine/src/` is already modular (clean `GenerateFindingsInput` to `GenerateFindingsResult`), but it has no formal reference section. Citations live only as inline tokens (`[[CODE:<atomId>]]`) validated against an allow-list. The build adds a deduplicated `references[]` to the output, minted from the cited atoms' provenance (the same atom records the gate returns), and a renderer for the formal reference block (section identifier, title, edition). The atom provenance to source it already exists end to end: `packages/retrieval/src/atom-trace.ts` exposes `sourceAdapter`, `sourceUrl`, `fetchedAt`, `contentHash`, section number, and edition. The identifier string format is the one open dependency on ICC (question 2); build it as a render parameter so the format finalizes on ICC's answer without reworking the pipeline.

Usage attribution wiring plus the scoped view (criterion 3). In hauska-mcp-server, `request_log.atom_ids_returned` is a real column but most read tools do not populate it; only `codex_findings_fetch` does. The helper `read-attribution.ts::atomIdsFromProvenance()` and `logToolRead()` exist but are not wired into `search_atoms`, `get_atom`, `atom_trace`, and the cortex read tools. The build wires those, then adds a per-atom unnest query and a scoped content-usage view filtered to ICC-derived atoms (identifiable by the `icc-model-code` tenant and the `icc-code-connect` source adapter, or a `did:hauska:code-section:icc-` prefix), grouped by title, section, surface, and query. The pay-per-query mechanism (meter, per-query price, would-charge, no charge) is a thin layer over that view. Atom title and section metadata live in hauska-engine, not the MCP Postgres, so the view either joins via the engine or carries the section number that the provenance entry already includes; prefer the provenance-carried section number to avoid a cross-service join.

## Workstreams, sequenced

The dependency that matters is no longer "wait for ingestion." It is "build the formal-reference contract first, because the module, the surfaces, and the Brief all consume it." That mirrors the execution plan's instruction to design citation first.

WS-0, fixtures for the dry run (hauska-engine, cc-agent-E). Hand-build a 2018 IPMC fixture and confirm or add a 2018 IBC fixture in the adapter's `__fixtures__`, representative enough to exercise the whole path. Reconcile to live payloads when Gate 1 lands. This unblocks an end-to-end dry run with zero mock data in the demo sense: the demo runs on the live spine; only the source payloads are fixtures until credentials arrive. Buildable now.

WS-B, formal reference (legacy-design-tools, cc-agent-C). Add `references[]` to the finding output and the formal-reference renderer per the citation design in [`02_execution_plan.md`](02_execution_plan.md). Identifier format as a parameter pending ICC question 2. Reconcile the inline tokens against the deduplicated references. Buildable now against existing atoms.

WS-C, plan-review module formalization (legacy-design-tools, cc-agent-C). Formalize `@workspace/finding-engine` to the contract: inputs gain explicit applicable ICC titles plus editions; outputs gain the `references[]` (WS-B) and per-query usage events; the gate-consumption path (`BRIEF_CODE_RETRIEVAL=gate`, `retrieveAtomsFromSubstrate`) is the standard, not the local-DB fallback. The engine is already LLM-agnostic and dependency-injected; keep its reasoning and any calibration out of the ICC-content partition (moat separation). Depends on WS-B.

WS-M, usage attribution plus scoped view (hauska-mcp-server, cc-agent-M). Wire `logToolRead()` across the read tools; add the per-atom unnest query and the ICC-scoped content-usage view; build the pay-per-query mechanism over it. Buildable now against existing atom traffic; scopes to ICC atoms once they flow.

WS-D, surface shells. D1 Brief ICC surfacing (hauska-brief-extension, extension agent): surface an ICC item with the formal citation inside the existing brief flow, reuse chrome; depends on the WS-B reference contract. D2 municipal IPMC shell and D3 B2B/architect IBC shell (legacy-design-tools `artifacts/plan-review`, cc-agent-C): thin shells over WS-C, differing only in titles passed and chrome; depend on WS-C.

WS-F, isolation and Administrator and wind-down (hauska-engine, cc-agent-E). Formalize the `icc-model-code` tenant scope plus accessPolicy for the demo instance, the Administrator runbook, access controls, and the teardown-equals-wind-down procedure. Prepare the live-ingestion runbook so that, when Gate 1 lands, going live is the documented credential flip plus assumption reconciliation, nothing more. Design now; the live flip is the one Gate-1-gated action.

WS-G, demo script and dry run (icc-demo, planner). The walkthrough from [`02_execution_plan.md`](02_execution_plan.md), rehearsed end to end on the live spine against the WS-0 fixtures, each step tagged to the three criteria. Run it before Gate 1; rerun on live content after.

## The one Gate-1-blocked action

Live ingestion only: populate `ICC_CODE_CONNECT_CLIENT_ID` and `ICC_CODE_CONNECT_CLIENT_SECRET`, reconcile the nine adapter assumptions against ICC's real OpenAPI spec, run `discover()`, filter to 2018 IBC and 2018 IPMC, ingest. Because WS-0 makes the path runnable on fixtures, this is a swap, not a dependency for the rest of the build. The critical path's first segment is therefore already done in code; what remains of it is purely credential-gated.

## Ground-truth corrections to carry

For the portfolio record. The gate exposes 62 tools across four products (public, codex, reporting, map), not the 46-across-three figure in the 2026-06-06 recon. The finding-engine runs Grok-first (`AIR_FINDING_LLM_MODE=grok` default; anthropic legacy; mock for CI), consistent with the Property Brief Grok-first note. The asserted-confidence baseline for `icc-code-connect` content is 0.78 with interval width 0.35, set in `packages/corpus/src/conformance/mint.ts`. These are observations from the 2026-06-22 source read, not decisions.
