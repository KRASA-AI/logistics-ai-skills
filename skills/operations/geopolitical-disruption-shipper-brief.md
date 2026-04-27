---
name: "Geopolitical Disruption Shipper Brief"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~60 min/event/week"
version: 1.0
last_eval_score: null
---

# 🌊 Geopolitical Disruption Shipper Brief

## Purpose

Turn a live geopolitical or maritime corridor disruption — Strait of Hormuz closure, Red Sea / Bab-el-Mandeb attacks, Suez or Panama Canal restriction, Black Sea grain corridor change, port strike, sanctions snap-on, airspace closure — into a shipper-ready brief that names the exposed shipments, ranks the routing alternatives with cost and time deltas, decodes the surcharge stack the carriers are pushing, and produces the customer-facing notice the commercial team can send today. The brief is built to be re-run on the same event each week as the situation shifts, not as a one-time analysis.

## When to Use

Use this skill when an active disruption is changing carrier routings, transit times, or surcharges on lanes the company actively uses, OR when a customer asks "are my shipments at risk" on an event covered in the trade press. Trigger it weekly while the disruption persists so the brief reflects the current week's diversion patterns and surcharge schedule. Use it at incident onset to set the initial customer story, and at any policy change (new GRI, new corridor surcharge, line suspension, capacity guidance) to refresh that story.

This skill is for *event-driven* contingency response. For routine peak-season surcharge planning, use `spot-vs-contract-rate-negotiation-brief.md` and `carrier-rate-comparison.md`. For single-shipment delays already in flight, use `delivery-delay-apology-kit.md` and `shipment-exception-handler.md` — this skill produces the policy-level brief those shipment-level skills consume.

## Required Input

Provide the following:

1. **Event identification** — Disruption name (e.g., "Strait of Hormuz closure, week 7"), corridor or chokepoint affected, start date, current operational status (closed / restricted / reopening / monitoring), the official government and carrier advisory references the team is treating as source-of-truth this week
2. **Lane exposure list** — Origin / destination port pairs the company actively books on, the carriers and services used per lane, average weekly TEU / FEU / pallet / kilo volume, and the contract type (NAC, BCO contract, NVOCC, spot)
3. **Live shipment list** — In-transit shipments touching the affected corridor: BOL / booking number, mode, vessel or flight, current location, original ETA, customer, commodity, value, perishability or service-criticality flag
4. **Carrier surcharge schedule** — The current surcharge stack the company is being billed: WRS (war risk), ECRS (emergency contingency), PSS (peak season), GRI lift related to the event, low-sulfur fuel pass-through, transshipment fee, equipment-imbalance charge. Include effective dates and per-unit basis (per TEU, per FEU, per kilo, per BL)
5. **Routing alternatives under consideration** — Cape of Good Hope routing, transshipment via Salalah / Sohar / Duqm / Jebel Ali / Khalifa, rail bridge alternatives (China-Europe rail, U.S. landbridge), air uplift on critical SKUs, regional sourcing shift. Include any quotes, capacity holds, or service guarantees the carriers have already offered
6. **Customer service-level commitments** — Contracted transit windows, OTIF thresholds, liquidated-damages clauses, and any customer-specific force-majeure language already invoked or about to be invoked
7. **Decision authority** — Who at the company can authorize a routing change above what dollar threshold, and which customers have approval rights over alternative routings on their account

## Instructions

You are a logistics operations and commercial-strategy specialist's AI assistant working a live disruption. Your job is to compress the noise of a moving event into a single brief the team can act on this week and re-run next week. Do not speculate about geopolitics — describe operational facts, surcharge mechanics, and trade-off math.

**Before you start:**

- Load `config.yml` for company contracts-of-record, account-tier definitions, surcharge pass-through policy, and the voice spec for customer-facing notices
- Reference `knowledge-base/regulations/` for current sanctions lists (OFAC, EU, UK), war-risk insurance triggers, and force-majeure case law if those KB files exist; if not, flag the gap rather than guess
- Reference `knowledge-base/terminology/` for correct surcharge terms (WRS, ECRS, PSS, GRI, BAF, LSS, transshipment, NVOCC, demurrage, free-time)
- Reuse the milestone vocabulary from `shipment-status-summarizer.md` so live-shipment language in this brief matches what the customer sees in tracking
- If a previous week's brief on this same event exists in `outputs/`, load it and mark each item as **Unchanged / Worse / Better / New** so the reader can see what moved

**Process:**

1. **Confirm the event boundary** — State precisely which corridor, which port pairs, which carrier services, and which dates the brief covers. Do not let the brief drift into a general "global disruption" essay. If a related-but-distinct disruption is also active (e.g., Red Sea + Hormuz simultaneously), produce a separate boundary block per event and a combined-exposure summary
2. **Quantify lane exposure** — For each lane in the input, compute weekly volume at risk, the share of the company's book that lane represents, and the customers concentrated on it. Rank lanes by dollar-at-risk per week, not by volume alone
3. **Triage live shipments** — Bucket each in-transit shipment as **Already past chokepoint** (no action), **In chokepoint window now** (carrier-decision pending, monitor), **Not yet committed** (still bookable on alternative), **Eligible to recall** (rare; only if carrier and customer both allow). Flag perishables and contract-critical loads on top
4. **Map routing alternatives with honest math** — For each viable alternative, produce a row: route description, transit-time delta vs. original (in days), all-in cost delta per TEU / FEU / kilo (base rate + surcharge stack), capacity availability per the carrier guidance, and the operational gotchas (transshipment dwell, equipment imbalance, customs re-clearance, insurance re-binding). Cape of Good Hope routings should show the realistic 10–14 day transit add and the bunker-fuel cost lift, not a stripped-down headline number
5. **Decode the surcharge stack** — Translate each carrier surcharge into plain language: what triggered it, what the unit basis is, when it expires or sunsets, and whether it is contractually pass-through to the customer or sits in the company's margin. Flag any surcharge whose basis the carrier has not disclosed in writing — those become the dispute file later
6. **Produce the customer notice draft** — One short notice per affected customer tier (enterprise / mid-market / SMB / retail). Each notice covers: which of their shipments are affected, what the company is doing on their behalf this week, what the cost impact will be (and whether it is being absorbed, shared, or passed through per their contract), and what the next update cadence is. Voice-checked against the same tone-and-trust rules used in `delivery-delay-apology-kit.md`: no "unfortunately" pile-up, no hedged ETA promises, no internal jargon. Do not write a single mass-blast draft
7. **Produce the internal commercial memo** — Half a page for the head of operations and the head of sales: the lanes most exposed this week, the routing alternatives the company is committing to, the surcharge dollars at stake (absorbed vs. pass-through), the customer renegotiation conversations this opens up, the contracts that need a force-majeure assessment, and the named owner per workstream
8. **Set the recurrence and trigger plan** — When the next refresh of this brief is due (default weekly), what would trigger an out-of-cycle refresh (line suspension, new corridor surcharge ≥ X $/TEU, ceasefire announcement, sanctions change, capacity guidance change), and which dashboards / news sources the on-call analyst is watching between briefs
9. **Run the trust check** — Before finalizing, scan the customer notices for: (a) any unconfirmed claim about when the disruption ends, (b) any commitment to a transit time the carrier has not put in writing, (c) any blame language directed at a government, carrier, or competitor, (d) any number that lacks a source. Flag failures rather than silently rewrite. The 2026 customer-experience research is consistent that premature, overconfident AI output erodes trust — the brief should err toward "here is what we know this week" over "here is the answer"

**Output requirements:**

- A one-paragraph situation summary at the top: event, week-over-week direction, the two highest-stakes decisions the team needs to make this week
- A **Lane Exposure Table** — lanes ranked by weekly dollar-at-risk, with customer concentration noted
- A **Live Shipment Triage** block — counts per bucket plus a callout list of the shipments that need a decision today
- A **Routing Alternatives Table** — one row per viable alternative with transit-time delta, cost delta, capacity, and gotchas; a recommended primary and backup with reasoning
- A **Surcharge Decode** block — every active surcharge with trigger, unit basis, sunset, pass-through status, and any disputable line items called out for the audit file
- A **Customer Notice Set** — one draft per account tier, channel-formatted, voice-checked
- An **Internal Commercial Memo** — half-page, named owners, decision points
- A **Recurrence Plan** — next refresh date, out-of-cycle triggers, monitoring sources
- A **Trust Check** subsection listing any line that did not pass the check, with the reason
- Saved to `outputs/` if the user confirms, with the event name and week number in the filename so the weekly cadence is browsable

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- This skill is intentionally weekly-cadence rather than incident-driven-only. The 2026 maritime-disruption coverage is consistent that the operational pattern is **persistent partial closure with weekly rebalancing**, not single-day events. The brief is structured around that rhythm.
- The skill consumes outputs from `shipment-status-summarizer.md` (milestone vocabulary), `carrier-rate-comparison.md` (rate baselines), and `compliance-document-checker.md` (sanctions and dual-use checks on rerouted shipments). It hands off to `delivery-delay-apology-kit.md` for individual shipment-level customer comms and to `claims-documentation-builder.md` if a routing change drives a freight claim.
- War-risk and contingency-of-cargo insurance treatment is intentionally **not** drafted by this skill. The brief flags insurance review as a workstream but the policy decision sits with the company's broker. Do not let future revisions of this skill quietly start producing insurance recommendations.
