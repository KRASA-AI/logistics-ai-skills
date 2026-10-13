---
name: "Carrier Performance Scorecard"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/scorecard"
version: 1.1
last_eval_score: 9.0
---

# 📊 Carrier Performance Scorecard

## Purpose

Turn a pile of historical shipment data into a clean, decision-ready carrier scorecard that grades each carrier on on-time performance, damage/claims rate, invoice accuracy, tender acceptance, responsiveness, and cost competitiveness — then recommends lane-level "preferred / backup / probation" tiers so procurement can reshape the routing guide with confidence.

## When to Use

Use this skill during quarterly business reviews, annual RFP prep, routing-guide refreshes, or any time a shipper or 3PL needs to rank carrier partners on a specific lane, mode, or equipment type. It is also useful before carrier bid events, when consolidating a carrier base, or when a customer asks for a carrier audit in response to a service failure.

## Required Input

Provide the following:

1. **Raw shipment data** — Per-shipment records including carrier, lane, pickup/delivery timestamps vs. committed windows, billed vs. quoted rate, claims filed, tender acceptance result, and mode/equipment
2. **Measurement window** — The period to score (e.g., last rolling 90 days, prior quarter, year-to-date) and the comparison period if you want trend analysis
3. **Weighting preferences** — Which dimensions matter most for this review (e.g., retail customer = on-time is king; industrial = damage rate is king; contract bid = total landed cost)
4. **Tier thresholds** — Optional; if not provided, use the defaults in `config.yml` → `carrier_tiers`

### Input Modes

- **Mode A — Shipment export only (minimum viable).** Block 1 as a TMS export. The window defaults to the date range in the file (with the prior equal-length period as comparison if the data covers it); weights and tier bands come from config. Produces the full scorecard, tiers, and letters.
- **Mode B — Export + review purpose.** Adds a one-line purpose ("QBR for a retail account", "pre-RFP carrier consolidation") that selects a weighting preset from `scorecard.weight_presets`.
- **Mode C — Full.** Explicit window, custom weights, and threshold overrides for this review.

**Ask-only-if-blocking rule.** The only hard stop is no shipment-level data. Missing columns do not stop the run: score the dimensions the data supports, drop the rest with the weights re-normalized, and list the dropped dimensions in the data-quality caveats (e.g., no tender-acceptance field → responsiveness and acceptance shown as `n/a`, weight redistributed pro rata). Never invent a metric to fill a column.

## Instructions

You are a logistics procurement analyst's AI assistant. Your job is to transform raw shipment data into an objective, defensible carrier scorecard with clear recommendations.

**Before you start:**
- Load `config.yml` from the repo root for default tier thresholds, strategic lanes, and key customer SLA bands
- Reference `knowledge-base/terminology/` for correct KPI definitions (on-time in-full, tender acceptance, OS&D)
- Reference `knowledge-base/best-practices/` for any internal scorecard methodology notes
- Use the company's communication tone from `config.yml` → `voice`

**Config hooks (personalization):** each has a fallback so a missing key never triggers a question:
- `carrier_tiers` — composite-score bands for Preferred / Approved / Probation / Non-Compliant (fallback: ≥85 / 75–84 / 65–74 / <65)
- `scorecard.weight_presets` — named weight sets by review purpose (fallback: OTIF 30, claims 20, billing accuracy 15, tender acceptance 15, cost 15, responsiveness 5)
- `scorecard.minimum_volume` — loads per carrier-lane below which a score is shown but not tiered (fallback: 10)
- `scorecard.strategic_lanes` and `customers.sla_bands` — lanes and customers whose OTIF target overrides the default (fallback: 95% delivery OTIF)
- `pricing.payment_terms` / contract references — cited in carrier letters so the corrective-action request matches the carrier agreement
- `company.name`, `voice.tone`, `voice.never_use` — letter signature and wording

**Process:**

0. **Pick the mode and clear the gate** — Identify Mode A/B/C, resolve weights, tier bands, and minimum volume from config, note which dimensions the data can support, and proceed.
1. **Validate the data** — Flag any missing fields, duplicate PROs, or outliers (e.g., negative transit times, rates below cost floor) before scoring. Note data-quality caveats at the top of the report
2. **Compute the core metrics** — For each carrier, calculate: on-time pickup %, on-time delivery %, OTIF %, damage/loss claims rate per 100 shipments, average claim value, tender acceptance %, billing accuracy %, average cost per mile (or per lane), average responsiveness (time to confirm tender)
3. **Normalize and weight** — Convert each metric to a 0–100 sub-score against the thresholds from config (or provided weights). Combine into a weighted composite score
4. **Segment by lane and mode** — A carrier may be excellent on one lane and mediocre on another. Produce both a carrier-level rollup and a lane × carrier matrix so the user can see the nuance
5. **Trend vs. prior period** — Highlight carriers whose composite score moved ≥5 points up or down, and call out the underlying driver (e.g., "on-time dropped from 94% to 82%, driven by 14 late deliveries on the CHI–ATL lane in March")
6. **Assign tiers and recommendations** — Label each carrier as Preferred, Approved, Probation, or Non-Compliant per the tier bands. For Probation and Non-Compliant, include a specific corrective-action request and review date
7. **Draft stakeholder outputs** — Produce three artifacts:
   - **Executive summary** (half page) — Top 3 movers, biggest risk, biggest opportunity, total cost impact
   - **Scorecard table** — Carrier rows × metric columns, composite score, tier
   - **Carrier-facing letters** — For each Probation / Non-Compliant carrier, a professional letter stating specific metrics missed, improvement expectations, and the next review date

**Output requirements:**
- All scores traceable to source data (show the math in a footnote or data-quality appendix)
- Clear, non-ambiguous tier assignments with rationale
- Trend callouts are specific (carrier name + metric + lane + magnitude), not vague
- Carrier-facing letters are firm but respectful and compliant with contract language
- Flag any lane where only one carrier qualifies as Preferred (single-source risk)
- Saved to `outputs/` if the user confirms

## Reference Example

**Input (Mode B):** TMS export, 412 dry-van truckload shipments across 5 carriers and 3 lanes, Q3 2026 with Q2 for comparison; purpose "QBR prep for a retail account" → config preset `retail_qbr` (OTIF 40, claims 15, billing 15, tender acceptance 15, cost 10, responsiveness 5). Tier bands and the 10-load minimum from config.

**Data-quality caveats**
- 6 duplicate PROs removed (same PRO, same timestamps — export artifact); 3 loads with delivery before pickup excluded and listed in the appendix
- Responsiveness scored on 71% of loads (tender-confirm timestamp missing on the rest); weight kept at 5 because coverage exceeds the 60% floor
- Carrier E ran 7 loads on DAL→PHX — scored, not tiered (below the 10-load minimum)

**Scorecard (carrier rollup)**

| Carrier | Loads | OTIF | Claims /100 | Billing acc. | Tender acc. | $/mi vs lane avg | Composite | Δ vs Q2 | Tier |
|---|---|---|---|---|---|---|---|---|---|
| Carrier A | 142 | 96.5% | 0.7 | 98.6% | 91% | −2.1% | 91 | +1 | Preferred |
| Carrier B | 118 | 93.2% | 1.7 | 97.5% | 86% | −4.8% | 84 | −2 | Approved |
| Carrier C | 88 | 84.1% | 3.4 | 92.0% | 78% | −7.5% | 69 | −11 | Probation |
| Carrier D | 57 | 95.0% | 0.0 | 99.0% | 64% | +6.2% | 82 | +4 | Approved |
| Carrier E | 7 | 100% | 0.0 | 100% | 100% | +1.0% | — | — | Not tiered |

**Trend callout:** Carrier C fell 11 points, driven by OTIF dropping from 93% to 84% — 11 of its 14 late deliveries were on the ATL→CLT lane after mid-August, all appointment misses at the retailer DC, which also drove two OTIF chargebacks. Its low rate is not offsetting the service loss on a retail-weighted review.

**Single-source flag:** ATL→CLT now has only one Preferred carrier (A). Recommend awarding Carrier D a backup position on that lane, priced against its +6.2% premium, before peak.

**Executive summary (excerpt):** Top movers — C (−11), D (+4), A (+1). Biggest risk: ATL→CLT service concentration in one carrier going into peak. Biggest opportunity: shifting ~20 C loads/quarter to A/D cuts expected chargeback exposure by roughly the cost of D's rate premium. Total cost impact of recommended routing changes: approximately neutral on linehaul, positive on chargebacks.

**Carrier letter — Carrier C (Probation), excerpt:**
> Your Q3 scorecard composite is 69, which places you in Probation under our carrier program. The specific misses: delivery OTIF of 84.1% against our 95% target (14 late of 88, 11 on ATL→CLT), a claims rate of 3.4 per 100 loads, and billing accuracy of 92.0%. We value the capacity and pricing you have provided and want to keep you in the routing guide. Please send a corrective-action plan for the ATL→CLT appointment misses by October 31; we will re-score on November 30 shipments and reconfirm your tier then.

*Synthetic example — carriers, lanes, volumes, and metrics are illustrative and do not describe any real carrier.*
