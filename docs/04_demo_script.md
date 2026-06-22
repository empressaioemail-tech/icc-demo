# Demo script and dry run (WS-G)

The live walkthrough for ICC, built on the surfaces that landed 2026-06-22 (all tested green): the formalized plan-review module and its two shells, the Brief ICC surfacing, the atom-grain usage metering, and the isolated demo instance. Every step runs on the live Hauska spine and MCP gate; the source content runs on hand-built 2018 IBC and 2018 IPMC fixtures until ICC credentials land (Gate 1), at which point the same walkthrough reruns unchanged on live Code Connect content. Each step below is tagged to the three acceptance criteria.

The demo is the content path and the UX, not visual polish. The operator shows samples of the real production surfaces alongside the functional demo. Do not expose our proprietary architecture, roadmap, calibration, or internals at any point; the surfaces show reasoned output and a formal reference, never the machinery.

## Pre-flight checklist

Confirm before ICC joins.

The isolated demo instance is up: the `icc-model-code` tenant is populated with the 2018 IBC and 2018 IPMC fixture atoms, `platform-internal` accessPolicy, and the designated Administrator holds the only platform-internal gate credential. Nothing in production depends on it.

Migration `0045_finding_run_code_references.sql` is applied to the demo deployment Neon (a merged migration is not auto-applied to the live Cloud Run Neon; finding-run reference persistence fails silently without it).

Retrieval routes through the gate: `BRIEF_CODE_RETRIEVAL=gate`, the surfaces present `X-Hauska-Key` (reporting product) and `X-Hauska-Access-Tier: platform-internal` so they resolve the ICC atoms. Run a single warm query and confirm the gate path resolves ICC atoms, not the local-DB fallback.

The usage views return rows: `content_usage.sql` (ICC-scoped panel) and `pay_per_query.sql` resolve against `request_log.atom_ids_returned`. Run one query first so the meter has something to show.

The Brief, the municipal shell, and the architect shell each load against the demo instance.

## The one-ledger frame (say this once, early)

The single strongest point to make, and to make explicitly: the formal citations ICC sees in steps 1 through 3 and the usage meter in step 4 are two renderings of one ledger. When the gate returns an ICC section to a surface, it records that section in `atom_ids_returned`. The formal reference is that record rendered as an ICC identifier; the usage meter is that same record counted. Citation correctness is therefore not the model's word, it is the gate's accounting, and the usage numbers are the same accounting. One ledger, two views, both auditable. This is what makes both criterion 1 and criterion 3 credible at once.

## Step 1, the Brief surfaces ICC content with a formal citation

Open the Brief on a property in the demo context. The Brief surfaces one IPMC 2018 property-maintenance condition as a reasoned line with a formal citation: section identifier, title, edition. The raw section body is never shown; the line is our interpretation plus a formal pointer, and the citation carries the ICC viewer deep link.

Criterion 1 (citation): the reference is formal and correct, and it matches the same reference shape used by the plan-review, so citations are consistent across surfaces. Criterion 2 (layer-in-between): a reasoned brief line with a pointer, not a code dump; the surface consumed the section through the gate and rendered intelligence over it, never the wholesale text.

## Step 2, the plan-review in the municipal context (IPMC)

Open the municipal shell and submit a property condition or context. The plan-review retrieves the applicable IPMC 2018 sections through the gate, reasons over them, and returns findings. Each finding carries an inline citation marker that resolves to the formal reference block at the bottom: identifier, title, edition, deduplicated, no section bodies.

Point out the integrity guarantee here: a finding can only cite a section that was actually retrieved, because the reference block is minted from the atoms the gate returned, not free-typed by the model. A citation that was not retrieved cannot appear.

Criterion 1 and criterion 2, on the municipal surface.

## Step 3, the plan-review in the architect context (IBC)

Open the architect shell and submit a design context. The same plan-review module, now passing IBC 2018, returns findings with IBC citations and the same formal reference block. Make the architectural point: this is one plan-review function serving two contexts, municipal property-maintenance and architect building-code, through one engine. The license scope is coherent, the Brief plus the plan-review across both contexts, not three bespoke products.

Criterion 1 and criterion 2, on the B2B surface, plus the one-module-two-contexts story.

## Step 4, usage tracking and the pay-per-query mechanism

Open the scoped content-usage view. It shows the per-title, per-section, per-surface, per-query meter accumulated from steps 1 through 3, drawn from the same gate ledger that minted the citations. Walk the granularity: usage resolves to the individual ICC section (each section is one atom), attributed to the surface that called it.

Then show the pay-per-query mechanism: the meter, a per-query price, and the resulting would-charge, with an explicit NOT-CHARGED state on every row. State plainly that the PoC forbids commercial use and that this is a demonstrated mechanism, not a transaction. The view is scoped to content usage only: ICC section counts, surface, query. It exposes no calibration and no internals.

Criterion 3 (usage tracking), and the close of the one-ledger frame: these counts are the same `atom_ids_returned` that produced the references in steps 1 through 3.

## Step 5, closing conversation (staged, not a demo element)

After the walkthrough, as conversation rather than a built screen, raise the larger idea: Hauska as ICC's monetization rail for the agent ecosystem. This is staged as the closing discussion, it is not part of the demo flow, and it is routed through a catalog-thesis-check before it is pitched. Keep it a conversation about direction, not a product claim.

## If something fails live

If a surface call errors mid-demo, fall back to the warm query from pre-flight (the result is already resolved) and narrate the path rather than re-running live. If the usage view is empty, it means no metered query has run in the window; run step 2 again and refresh. Do not improvise around the content boundary: never display a raw section body to recover from a failure.

## Mapping to the deliverable

This script satisfies execution-plan deliverable 2 (the demo walkthrough mapped to the three criteria). The citation design it exercises is in `docs/02_execution_plan.md` and was built and tested per the closes in `doc_repo/_inbox` dated 2026-06-22. The only step that changes when Gate 1 lands is the source content: the fixtures become live Code Connect 2018 IBC and IPMC, and the walkthrough is identical.
