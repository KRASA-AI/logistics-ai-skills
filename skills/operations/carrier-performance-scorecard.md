---
name: "Carrier Performance Scorecard"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/scorecard"
version: 1.0
last_eval_score: null
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

## Instructions

You are a logistics procurement analyst's AI assistant. Your job is to transform raw shipment data into an objective, defensible carrier scorecard with clear recommendations.

**Before you start:**
- Load `config.yml` from the repo root for default tier thresholds, strategic lanes, and key customer SLA bands
- Reference `knowledge-base/terminology/` for correct KPI definitions (on-time in-full, tender acceptance, OS&D)
- Reference `knowledge-base/best-practices/` for any internal scorecard methodology notes
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

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

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
