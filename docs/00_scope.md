# ICC Code Connect PoC — demo scope

## What this is and where it sits

We signed the ICC PoC Agreement for the Code Connect API. The PoC is the evaluation stage before a 12-month SaaS license; commercial use only becomes possible after the SaaS agreement. Term is 180 calendar days. ICC's PM is Ed Cilurso; the intake call is done. The deliverable is a live product demonstration of how ICC content surfaces inside our applications and how we handle it after consuming it through the API. Not a deep technical review; not a written report; do not expose our proprietary architecture, roadmap, or internals.

## Licensed materials (the only content available)

- 2018 International Building Code (IBC)
- 2018 International Property Maintenance Code (IPMC)

Both via the Code Connect API. Additional titles require a written amendment. Assume nothing else.

## Decisions locked

1. Demo surfaces = license scope. (a) The Brief extension, and (b) a generalized AI plan-review function reused across municipal and B2B surfaces. The plan-review is the natural home for both titles: IBC building-code checks in the B2B/architect context, IPMC property-maintenance checks in the municipal context, through one engine. The demo shows the plan-review serving both contexts plus the Brief surfacing ICC content. This is a deliberately broad, coherent license scope: ICC's focus becomes the Brief and the plan-review function across both contexts.
2. Pay-per-query: mechanism plus staged pitch. Show real usage tracking plus the pay-per-query flow (meter to per-query price to would-charge) as a demonstrated mechanism, NOT a live transaction (the PoC forbids commercial use). The "Hauska as ICC's monetization rail for the agent ecosystem" idea is staged as the closing conversation, not a core demo element, and is routed through a catalog-thesis-check before it is pitched.
3. Deployment: isolated demo instance. ICC content sandboxed in a separate instance with a designated Administrator; tear-down equals wind-down; nothing in production depends on PoC access.

Build posture: use the LIVE spine and MCP (no mock data). Little to no visual design; the demo is UX and content-path focused. The plan-review is constructed for reuse beyond this demo (formalize the existing plan-review shape into a durable module).

## Three acceptance criteria (map the demo to all three)

1. Correct citation. Every answer traces to the correct ICC source in a clean, formal reference section (section identifier + title + edition). Agent reasoning over the content is fine; the reference must be formal and correct. Highest-scrutiny item — design it first.
2. Layer in between. Our intelligence always sits between the user and the raw code. We never republish or expose the code wholesale. The plan-review reasons and cites; it never dumps the code.
3. Usage tracking. Visible, credible per-query / per-section / per-surface tracking — the foundation for the usage-based pay-per-query model.

## Hard constraints (PoC agreement)

- Internal use and evaluation only; no commercial or external use of content/API until the SaaS agreement.
- ICC owns all rights including derivative works against their content; derivatives must be transmitted to ICC. Keep our proprietary reasoning and calibration SEPARATE from ICC-content-derived atoms; do not entangle our moat as ICC-owned derivatives.
- Security: designate an Administrator for API-received content; prevent unauthorized access.
- Wind-down: at PoC end or on ICC request, return materials and stop using the API; nothing in prod may depend on PoC access.
- Provided as-is, no warranty.

## Deliverables to produce (the execution plan)

1. Workstreams for each surface (Brief + generalized plan-review across municipal and B2B), sequenced, dependencies named.
2. The demo script / walkthrough run for ICC, each step mapped to the three acceptance criteria.
3. The citation and formal-reference design (highest scrutiny): the reference section shape, ICC identifiers used, how inline citations link to it, how agent reasoning sits above it.
4. The usage-tracking surface and the pay-per-query mechanism: the scoped content-usage view, how it connects to event logging (gate metering + F1 atom-grain attribution), the flow shown (no live charge).
5. Open questions for ICC's technical team to resolve before the demo (seeds below).
6. A 180-day timeline with the critical path flagged.

## Open questions for ICC's technical team (seed)

- Access mechanism: is it the same API for the PoC and live? Section-level retrieval or whole-title? A link to scrape or an API/DB? (Operator has already asked Ed; awaiting reply — this is the first critical-path gate.)
- Rate limits / quota during the PoC.
- Content versioning and updates.
- The citation identifier format ICC expects in the formal reference.
- Any caching or storage restrictions on consumed content (bears on the isolated-instance design and wind-down).
