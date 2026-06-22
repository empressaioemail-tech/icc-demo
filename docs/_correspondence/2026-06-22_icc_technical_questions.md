# ICC technical questions — sent 2026-06-22

Sent to Ed Cilurso (ICC PM) on 2026-06-22, to route to ICC's technical team. Question 1 (access mechanism) is the first critical-path gate and was raised earlier; it is restated here so the full batch lives in one place. Answers logged below as they return.

## Questions as sent

1. Access mechanism (top priority). Is the Code Connect API the same interface for the PoC and the eventual live SaaS stage, or do they differ? Retrieval granularity: can we request specific sections, or is access at the whole-title level? Delivery: REST API, database, or file/link export?
2. Citation identifier format. When we cite a section in a formal reference, what canonical identifier format does ICC expect, for each of 2018 IBC and 2018 IPMC?
3. Verbatim display boundary. In a citation or reference context shown to an end user, how much section text, if any, may be displayed under the PoC, and under the eventual display license? Our default is section identifier and heading alongside our own analysis, no full section body.
4. Rate limits and quota during the PoC term.
5. Content versioning and updates to the 2018 editions during the term, and how delivered.
6. Caching and storage restrictions on consumed content (bears on the isolated-instance design and clean wind-down).
7. Derivative works: ICC's working definition of a derivative of the content, as distinct from independent analysis that cites the content.
8. Wind-down mechanics: what "return materials and stop using the API" requires technically at PoC end.

## Workstream each answer unblocks

- Q1 to WS-A (ingestion) and the critical path.
- Q2 to WS-B (citation build), the identifier render.
- Q3 to WS-B and WS-D1 (Brief surfacing), the verbatim boundary.
- Q4 to WS-A and the usage-demo volume.
- Q5 to content-update handling.
- Q6 to WS-F (isolation, wind-down).
- Q7 to the moat-separation boundary (technical scope; legal definition routes to Nick).
- Q8 to WS-F (wind-down procedure).

## Answers log

Pending. Record each answer here with the date received, then propagate to the affected workstream in [`02_execution_plan.md`](../02_execution_plan.md) and [`03_build_plan.md`](../03_build_plan.md).

- Q1 access mechanism: pending.
- Q2 citation identifier format: pending.
- Q3 verbatim display boundary: pending.
- Q4 rate limits / quota: pending.
- Q5 content versioning: pending.
- Q6 caching / storage: pending.
- Q7 derivative-works definition: pending.
- Q8 wind-down mechanics: pending.
