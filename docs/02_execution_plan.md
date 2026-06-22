# Execution plan

Built on the locked decisions in [`00_scope.md`](00_scope.md) and the architecture in [`01_architecture_scaffold.md`](01_architecture_scaffold.md). This plan designs to those decisions; it does not relitigate them. It produces the six deliverables called for in scope: per-surface workstreams with dependencies, the demo script mapped to the three acceptance criteria, the citation design (first, highest scrutiny), the usage-tracking and pay-per-query design, the open questions for ICC's technical team, and a term-anchored timeline with the critical path flagged.

Two design ideas carry most of the weight and should be read first.

The first: the gate's metered atom ledger is one source of truth serving two acceptance criteria at once. F1 attribution populates `request_log.atom_ids_returned` with exactly the ICC sections the gate returned for a call. The formal reference (criterion 1, citation) is that list rendered as ICC identifiers. The usage meter (criterion 3, tracking) is that same list counted. Citation correctness is therefore not the model's word; it is the gate's accounting, rendered two ways. We should foreground this with ICC: one ledger, two renderings, both auditable.

The second: the plan-review is the reusable artifact, not a demo prop. We formalize the existing finding-engine shape into a surface-independent module with a fixed contract, and the municipal and B2B surfaces become thin shells that differ only in which titles they pass and which chrome they wear. The demo is the formalization milestone for the plan-review function that later reconciles into the architecture homes.

## Citation and formal-reference design (design first)

This is acceptance criterion 1, the highest-scrutiny item, and the gate to most build work. It is designed before the module so the module is built to its contract.

The reference derives from atom provenance, never from model free-text. Each ingested IBC or IPMC section becomes a code-section atom in the isolated demo partition. The atom carries an ICC provenance object: source title (IBC or IPMC), edition (2018), the canonical ICC section identifier, the section heading, and an ICC-owned marker. This provenance is the single source of truth for the citation. The exact identifier string format is an open question for ICC and is on the critical path for this workstream.

Inline markers are generated, not typed. In a finding, every claim that rests on a code section carries an inline marker (for example a bracketed `IBC 1004.5`) generated from the cited atom's provenance, not authored by the model. The model cannot emit a citation for a section it did not retrieve, because the marker is minted from the atom record the gate returned. This is the structural anti-hallucination guarantee, and it is what makes "correct citation" credible rather than asserted.

The formal reference section is a reconciled render of the gate ledger. At the bottom of each plan-review output and each Brief surfacing, a formal reference section lists every cited section once: identifier, full title, edition. The list is reconciled against `atom_ids_returned` for that call, so the reference is provably the set of ICC sections the gate actually metered. No section appears in the reference that was not retrieved; no retrieved-and-cited section is missing.

Reasoning sits above the citation; the code is never republished wholesale. The finding text is our interpretation and judgment. The reference points to the source. The finding does not paste the section body. How much verbatim section text, if any, may appear alongside a citation is a display-license question for ICC and is listed below. The default posture is identifier plus heading in the reference, interpretation in the finding, no wholesale body. This is the layer-in-between guarantee (criterion 2) made structural rather than promised.

Confidence is a read-contract object, asserted-with-provenance at PoC. Each finding carries a confidence object sourced from the read-contract: a value, a basis (asserted-with-provenance during the PoC, since calibration is sparse), and a verification state. This honors structural commitment 2 without claiming earned calibration we do not yet have on ICC content. The calibration logic itself is ours and stays out of the ICC-derived partition (see the moat-separation note below).

## The reusable plan-review module

We formalize the existing shape (the cortex-api finding-engine, the L-surface, the `codex_finding_generation` MCP tool) into a durable, surface-independent module with this contract.

Inputs: a submission or context, the jurisdiction, and the applicable ICC titles with editions (IBC, IPMC).

Process: retrieve the applicable code atoms via the live MCP gate (never the engine directly), reason over them, and produce findings. Each finding carries a formal ICC citation minted per the design above and a read-contract confidence object.

Outputs: findings, a formal reference section, and per-query usage events.

The two demo surfaces are thin shells over this one module. The municipal shell passes IPMC and wears municipal chrome; the B2B/architect shell passes IBC and wears architect chrome. Nothing else differs. This is what lets us tell ICC the license scope is one coherent function (plan-review) across two contexts plus the Brief, not three bespoke builds.

Moat separation is an architectural boundary, not a comment. The ICC-content-derived atoms (the ingested sections and any structured extraction directly of their content) are ICC-owned, live in the isolated partition, and are transmittable to ICC under the derivative-works clause. Our finding logic, our confidence calibration, and any calibration atoms are ours, authored by our engine, and live outside that partition. The finding output references ICC atoms; it is not entangled as an ICC-owned derivative of their content. The precise line between "a derivative of ICC content" (theirs) and "our independent interpretation that cites ICC content" (ours) is legally sensitive; the technical boundary is designed here, but the legal definition routes to Nick and is seeded as an ICC technical-team clarification below.

## Workstreams

Seven workstreams. WS-A is foundational and blocks most others; it is itself blocked on the first ICC gate.

| ID | Workstream | Depends on | Notes |
|----|-----------|-----------|-------|
| WS-A | ICC ingestion into isolated partition: wire IccCodeConnect adapter to the live Code Connect API, ingest IBC + IPMC as provenance-bearing code-section atoms, designate Administrator, set access controls | Gate 1 (ICC access-mechanism answer) | Foundational; nearly everything depends on it |
| WS-B | Citation and formal-reference build: provenance schema, inline-marker minting from atom records, formal reference renderer, reconciliation against `atom_ids_returned` | WS-A schema, ICC identifier-format answer | Design is complete above; this is the build. Feeds WS-C |
| WS-C | Plan-review module formalization to the contract above; consumes via gate | WS-A (atoms flowing), WS-B (citation contract) | The reusable artifact |
| WS-D1 | Brief extension ICC surfacing: surface an ICC item with a formal citation inside the existing brief flow, reuse chrome | WS-A, WS-B | Thin |
| WS-D2 | Plan-review municipal shell (IPMC) | WS-C | Thin shell |
| WS-D3 | Plan-review B2B/architect shell (IBC) | WS-C | Thin shell |
| WS-E | Usage tracking and pay-per-query: scoped content-usage view from gate metering + F1, the meter-to-price-to-would-charge mechanism, no live charge | WS-A (metered calls), real usage from WS-D | View can build in parallel once atoms flow; needs surfaces for credible numbers |
| WS-F | Isolation, security, wind-down: isolated instance hardening, access controls, Administrator runbook, teardown-equals-wind-down procedure | WS-A | Spans WS-A; finalized before demo |
| WS-G | Demo script and dry runs on the live spine | WS-D, WS-E | Rehearsal gates the demo |

Sequence in one line: Gate 1 unblocks WS-A; WS-A plus the ICC identifier answer unblocks WS-B; WS-A and WS-B unblock WS-C; WS-C unblocks the D2/D3 shells while WS-A and WS-B unblock D1; WS-E builds in parallel once metered calls flow and finalizes against real surface usage; WS-F runs alongside WS-A and closes before the demo; WS-G rehearses against everything.

## Demo script mapped to the three acceptance criteria

A live walkthrough on the spine, no mock data. Each step is tagged to the criteria it demonstrates. The operator shows samples of the real production surfaces alongside the functional demo shells.

Step 1, Brief surfacing. Open the Brief on a Central TX property. The brief surfaces an ICC-relevant item (an IPMC property-maintenance condition) as a reasoned brief line with an inline marker and a formal reference. Demonstrates criterion 1 (the citation is formal and correct) and criterion 2 (a reasoned brief item with a pointer, not a code dump).

Step 2, plan-review in the municipal context. Submit a property condition or context to the municipal shell, which passes IPMC. The module returns findings, each with an inline marker resolving to the formal reference section. Criterion 1 and criterion 2.

Step 3, plan-review in the B2B/architect context. Submit a design submission to the architect shell, which passes IBC, against the same module. Findings carry IBC citations. This shows one module serving two contexts (the coherent license scope) on top of criteria 1 and 2.

Step 4, usage tracking and the pay-per-query mechanism. Open the scoped content-usage view. It shows the per-title, per-section, per-surface, and per-query meter accumulated from steps 1 through 3, drawn from the same gate ledger that minted the citations. Show the pay-per-query mechanism (meter, per-query price, would-charge) with an explicit not-charged state. Criterion 3, and the chance to make the one-ledger point: these counts are the same `atom_ids_returned` that produced the references in steps 1 through 3.

Step 5, closing conversation (staged, not a core demo element). The "Hauska as ICC's monetization rail for the agent ecosystem" idea, surfaced as conversation, not demo. This is routed through a catalog-thesis-check before it is pitched and is not built into the demo flow.

## Usage tracking and pay-per-query

Source. The live gate meters every call. F1 atom-grain attribution gives the per-atom (per ICC section) DID list in `request_log.atom_ids_returned`. Because each ICC section is one atom, the meter is section-grained for free, which is exactly the per-section credibility ICC asked for.

The scoped view. Filter the ledger to ICC-derived atoms. Group by title (IBC, IPMC), by section, by surface (the consuming product key or surface shell), and by query. Show call counts at each grain. The view is scoped to content usage only: ICC atom counts, surface, and query. It does not expose our calibration or atom internals.

The mechanism. Attach a per-query price (a demo placeholder; the real pricing model is settled elsewhere and not part of this PoC). Compute would-charge as a function of metered queries (or per-atom-grain, if we want to show section-level pricing) and display it with an unmistakable not-charged banner. The PoC forbids commercial use; this is a demonstrated flow, never a transaction.

## Open questions for ICC's technical team

The first is the gating critical-path item. The rest unblock specific workstreams and should go to Ed in one batch behind the access-mechanism answer.

1. Access mechanism (Gate 1, blocks WS-A and the critical path). Same API for the PoC and the live SaaS stage? Section-level retrieval or whole-title? A link to scrape, or an API/DB? Operator has asked Ed; awaiting reply.
2. Citation identifier format (blocks WS-B). The exact section-identifier string ICC expects in a formal reference (for example the precise rendering of a 2018 IBC section number).
3. Verbatim-display boundary (feeds WS-B and WS-D1). How much, if any, verbatim section text may appear alongside a citation under the PoC and the display license. Our default is identifier plus heading, interpretation in the finding, no wholesale body.
4. Rate limits and quota during the PoC (feeds WS-A ingestion and the usage demo volume).
5. Content versioning and update mechanics (edition is fixed at 2018; how are corrections or updates delivered).
6. Caching and storage restrictions on consumed content (feeds the isolation design and the wind-down: what may persist in the partition and for how long).
7. Derivative-works boundary (feeds the moat-separation boundary and routes the legal definition to Nick). ICC's working definition of a derivative of their content versus our independent interpretation that cites it, so the partition boundary is scoped correctly.
8. Wind-down mechanics (feeds WS-F). What "return materials and stop using the API" requires technically: partition deletion plus confirmation, or more.

## Timeline within the 180-day term, critical path flagged

The term is a hard 180 calendar days; that is the only fixed duration. Rather than fabricate per-task day counts, the plan is sequenced into gated phases anchored to the two real external dates (Ed's access-mechanism reply, and the demo date as the term-bounded endpoint) and to the dependency chain above. If you want hard calendar targets per phase instead, say so and I will set them against a chosen demo date.

Phase 0, the gate. Resolve Gate 1 (ICC access mechanism). Send open questions 2 through 8 in the same batch so answers land before their workstreams start. Nothing on the critical path proceeds until the access mechanism is known. This phase is pure waiting-on-ICC plus question batching, so it starts now.

Phase 1, foundation. On Gate 1: stand up the isolated partition, wire the adapter to the live Code Connect API, ingest IBC and IPMC with provenance, designate the Administrator (WS-A and the start of WS-F). On the identifier-format answer: build the citation and formal-reference layer (WS-B). These two run together because WS-B needs the WS-A schema plus the ICC identifier answer.

Phase 2, the module. Formalize the plan-review module to the contract (WS-C). Begin the scoped usage view (WS-E) as soon as metered calls flow.

Phase 3, surfaces. Build the three thin shells (WS-D1 Brief, WS-D2 municipal, WS-D3 architect). Finalize the usage view and pay-per-query mechanism against real surface traffic (WS-E).

Phase 4, harden and rehearse. Close WS-F (isolation, security, Administrator runbook, wind-down procedure). Write and dry-run the demo script on the live spine (WS-G). Route the closing monetization conversation through catalog-thesis-check.

Phase 5, demo and wind-down readiness. Run the demo. Keep the wind-down procedure tested so tear-down equals wind-down on PoC end or ICC request, with nothing in production depending on PoC access.

Critical path: Gate 1, then WS-A ingestion, then WS-C module, then the WS-D shells, then WS-G dry-run, then the demo. WS-B (citation) feeds WS-C and shares Phase 1 with WS-A. WS-E (usage) is parallelizable after atoms flow but must be demo-ready by Phase 3. The single longest pole is Gate 1: every downstream phase waits on the ICC access-mechanism answer, which is why it is the first and only thing happening now.

## What this plan does not do

It does not commit build dispatches; those follow operator review of this plan. It does not work the legal definition of the derivative-works boundary (routes to Nick) or relitigate the locked surface, pay-per-query, or isolation decisions. It does not pitch the monetization rail (staged, and gated on catalog-thesis-check).
