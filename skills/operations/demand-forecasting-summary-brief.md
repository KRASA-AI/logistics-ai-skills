---
name: "Demand Forecasting Summary Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~40 min/cycle"
version: 1.1
last_eval_score: 8.7
---

# 📈 Demand Forecasting Summary Brief

## Purpose

Turn a raw demand forecast (volume by lane, SKU, or region) and recent actuals into a short operations brief that planners, carrier-sourcing leads, and warehouse managers can act on: where volume is trending up or down, where the forecast is diverging from reality, where capacity or inventory needs to move, and what the forecast implies for carrier commitments and labor plans over the next 2–8 weeks.

## When to Use

Use this skill for the weekly S&OP or transportation-planning huddle, at the start of each month's capacity commitment cycle, when a seasonal peak is approaching (produce, back-to-school, holiday, returns), when a new customer onboarding adds volume that will shift the baseline, or any time the planning team needs to translate a statistical forecast into on-the-ground decisions that dispatch, yard, and procurement teams understand.

## Required Input

Provide the following:

1. **Forecast dataset** — Volume (loads, pallets, units, or TEU) by lane, SKU family, DC, or region for the next 4–8 weeks; include the prior forecast if available so drift is visible
2. **Recent actuals** — The last 4–8 weeks of actuals in the same grain, so forecast accuracy (MAPE or bias) can be computed
3. **Known demand drivers** — Promotions, new account onboards, product launches, customer contract changes, or planned inventory shifts that are already baked into the forecast
4. **Capacity context** — Current committed carrier capacity per lane (contracted MCQ, routing-guide primary/secondary), DC throughput limits, and current on-hand inventory at key nodes
5. **Planning objective** — What decision this brief is feeding: weekly carrier allocation, pre-positioning move, peak-season hiring, routing-guide refresh, or an S&OP readout

## Instructions

You are a demand-and-supply planner translating a forecast into the operational moves that need to happen this week and next. Your job is to distill the forecast into a brief that stops being about statistics and starts being about action — while still being honest about uncertainty.

**Before you start:**

- Load `config.yml` for default MAPE tolerance, forecast grain, and planning horizon
- Reference `knowledge-base/terminology/` for correct terms (MAPE, bias, MCQ, primary/secondary tender, pre-positioning, safety stock, lane, S&OP)
- Confirm the audience — operations vs. sales vs. finance — the emphasis differs

**Process:**

1. **Sanity-check the inputs** — Scan the forecast for obvious anomalies (a 300% week-over-week jump on a single lane with no known driver, negative volumes, missing weeks). Flag anything that looks like a data glitch before interpreting it
2. **Compute accuracy against recent actuals** — Calculate week-over-week MAPE and directional bias (over- or under-forecasting) at the grain of the brief's audience. Call out the lanes, SKUs, or regions with the worst accuracy and identify the likely cause (promo not reflected, new customer ramp, seasonality mismodeled)
3. **Identify the top movements** — Rank the top 5–10 volume movements for the forecast horizon: largest upside lanes, largest downside lanes, and lanes crossing a capacity threshold (e.g., forecast exceeds primary-carrier MCQ)
4. **Map volume to capacity and inventory** — For each top movement, map it against current committed capacity and inventory. Flag where a lane is about to exceed primary-carrier MCQ (spot exposure coming), where a DC is approaching a throughput limit (overtime or weekend shift), where a downstream node is at risk of stockout, and where safety stock could be reduced to free working capital
5. **Recommend operational moves** — Produce a ranked set of moves with owners and timelines:
   - **Allocate capacity** — Raise MCQ with a secondary carrier, post to a mini-bid, or pre-commit a dedicated asset
   - **Pre-position inventory** — Move X pallets from DC A to DC B by date D ahead of the upside
   - **Re-plan labor** — Add a shift or temp labor at a named site for a named week
   - **Reset the forecast** — Refit the model or apply a manual override where accuracy is poor and the driver is known
6. **Quantify the downside and the guardrails** — Note the size of the spot-market exposure or stockout risk if no action is taken, and the cost of the proposed action, so the decision-maker can weigh them
7. **Draft the distribution** — Produce a short written brief (≤ 1 page) for the planning huddle and a 3–5 bullet Slack or email update for the broader team. Keep jargon appropriate to the audience; finance and sales will not recognize "MAPE" without context

**Output requirements:**

- A one-paragraph executive summary at the top: forecast horizon, top movement, biggest risk, single recommended action
- Accuracy section showing MAPE and bias for the last window, with 2–3 worst offenders called out
- Top movements ranked in a small table (lane/SKU, next 4 weeks, delta vs. prior forecast, capacity or inventory implication)
- Recommended moves in bullet form, each with owner, timeline, and estimated impact
- A confidence note — which parts of the forecast are high-confidence vs. which deserve a manual override
- Ready-to-send short-form update for the broader team in its own section
- An internal-notes block with data snapshot timestamps and any assumptions made
- Saved to `outputs/` if the user confirms

## Example Output

**Scenario:** Weekly S&OP brief for a mid-size 3PL operating mixed parcel + LTL + drayage volumes for two retail and one consumer-electronics customer. Cycle covers four-week forward horizon (2026-05-25 → 2026-06-21) with three drivers in play: Q2 retail-restock (Northeast DC fill), back-to-school early ramp on parcel (West Coast outbound), and post-Lunar-New-Year inbound drayage normalization at LGB / LAX. Forecast grain is lane-week; baseline forecast came from the planner's model on 2026-05-22 EOD; actuals through 2026-05-23 EOD. Audience: ops + procurement + finance, weekly huddle Tuesday 09:00 CT.

---

**Executive summary**

> Four-week forecast (2026-05-25 → 2026-06-21) is broadly on-trend across all three modes, but two lane-weeks need action this week: BOS→PHL LTL is forecasted 162% of prior week (back-to-school early signal on the consumer-electronics account, confirmed driver) and is **48% above primary-carrier MCQ** on weeks of 06-08 and 06-15 — spot exposure ~$11.4K if uncovered. Drayage at LGB→ONT projects 12% below baseline as the post-LNY normalization completes, freeing 28 chassis-days of committed capacity that can be redeployed to PHL→ATL where reefer demand is over-running the routing-guide primary. **One recommended action: open a mini-bid on BOS→PHL LTL for weeks of 06-08 and 06-15 by Thursday EOD to cover the MCQ overage at contract-equivalent rates.** Forecast accuracy on parcel ramp is the weakest spot (MAPE 18.4%, under-forecast bias) and warrants a manual override on the consumer-electronics SKU family before next week's pull.

**Accuracy section**

| Window | MAPE | Bias | Notes |
|---|---|---|---|
| Last 4 weeks (parcel — all customers) | 11.2% | +2.1% (slight over) | Within tolerance (config: 15% MAPE, ±5% bias) |
| Last 4 weeks (LTL — all customers) | 9.8% | +0.4% | Within tolerance |
| Last 4 weeks (drayage LGB / LAX) | 8.6% | -1.2% | Within tolerance |
| **Parcel — consumer-electronics SKU family (last 2 wks)** | **18.4%** | **-14.2% (under)** | **Out of tolerance — back-to-school promo on a new SKU not in the baseline; refit needed** |
| **LTL — BOS→PHL (last 2 wks)** | **22.1%** | **-19.6% (under)** | **Out of tolerance — same back-to-school driver downstream into LTL leg** |
| Drayage — LGB→ONT (last 2 wks) | 6.4% | +3.8% | Slight over-forecast; post-LNY normalization completing on plan |

**Top movements (next 4 weeks)**

| Rank | Lane / SKU | Wk 1 (06-01) | Wk 2 (06-08) | Wk 3 (06-15) | Wk 4 (06-21) | Δ vs. prior fcst | Capacity / inventory implication |
|---|---|---|---|---|---|---|---|
| 1 | LTL BOS→PHL | 22 loads | 38 | 41 | 29 | +162% wk 2, +148% wk 3 | **MCQ breach** wk 2 & 3 (primary contract MCQ 28/wk). Spot exposure ~$11.4K |
| 2 | Parcel WCD outbound (consumer-electronics) | 4,800 units | 7,200 | 8,100 | 6,400 | +52% (rolling) | Within DC throughput; sort-line OT wk 2 & 3 ~$3.2K |
| 3 | Drayage LGB→ONT | 86 loads | 79 | 74 | 78 | -12% | Frees 28 chassis-days; PacWest Intermodal commitment can be re-flexed |
| 4 | Reefer PHL→ATL | 14 loads | 19 | 22 | 18 | +28% | Routing-guide primary at MCQ ceiling wk 3; secondary stack ready |
| 5 | LTL ATL→MIA | 31 loads | 30 | 28 | 32 | -3% (flat) | Within MCQ; no action |
| 6 | Parcel Northeast DC fill | 2,100 units | 2,400 | 2,600 | 2,200 | +14% (rolling) | Within DC throughput |
| 7 | LTL CHI→DAL | 18 loads | 19 | 21 | 20 | +4% | Within MCQ; no action |

**Recommended operational moves**

1. **Allocate capacity — BOS→PHL LTL mini-bid for weeks of 06-08 and 06-15.** Owner: K. Mahoney (procurement). Timeline: post by 2026-05-28 EOD, close 2026-06-02 EOD, award by 2026-06-04. Estimated impact: covers 13 + 13 = 26 loads of MCQ overage at contract-equivalent rates (~$650/load avg vs. $1,090/load spot benchmark on the lane); avoided spot exposure ~$11.4K. *Confidence: high; driver is confirmed and a 2-week window is operationally comfortable for a mini-bid.*
2. **Re-flex drayage — release 28 chassis-days of committed PacWest Intermodal capacity at LGB→ONT to free working capital.** Owner: R. Singh (drayage desk). Timeline: notice to PacWest by 2026-05-26 EOD per the 7-day flex clause in the 2026 agreement; reallocate freed chassis-days to the PHL→ATL reefer lane via the cross-modal balancing run. Estimated impact: $4.2K saved on committed-but-unused drayage capacity; $1.8K avoided routing-guide-secondary premium on PHL→ATL reefer wk 3. *Confidence: moderate-high; depends on PacWest accepting the flex without arguing the 7-day window.*
3. **Re-plan labor — add a single Saturday-shift sort line on parcel WCD outbound, weeks of 06-08 and 06-15.** Owner: J. Ortega (WCD ops manager). Timeline: post the shift in the labor pool by 2026-05-29 EOD; confirm with the temp agency by 2026-06-03. Estimated impact: ~$3.2K labor cost; avoided ~$8.4K of pulled-forward-into-Friday OT plus protects on-time on the consumer-electronics account (Tier-1, no missed-MABD slack). *Confidence: high; same playbook ran on the Q4 peak with the same agency.*
4. **Reset the forecast — manual override on consumer-electronics SKU family (parcel) for the next 4 weeks at +25% over the model baseline.** Owner: D. Lin (demand planning). Timeline: in tonight's run (2026-05-26 EOD) before tomorrow's huddle. Estimated impact: reduces forward MAPE on the SKU family from projected 18% to projected 7%; downstream BOS→PHL LTL signal will tighten in next week's pull. *Confidence: moderate; the override is a placeholder until the planner can re-fit the seasonality term with the new SKU.*

**Confidence note**

- **High confidence:** drayage normalization at LGB→ONT (post-LNY pattern is well-established and the actuals are tracking the seasonal curve); BOS→PHL LTL upside (downstream of a confirmed promo, customer-confirmed PO cadence)
- **Moderate confidence:** parcel consumer-electronics SKU ramp (new SKU, two weeks of actuals, manual override pending model refit)
- **Lower confidence — manual review:** PHL→ATL reefer +28% (driver not yet confirmed; could be a one-off retail re-stock or a structural shift to a new produce-season lane — flag for planner follow-up with the customer's procurement contact)

**Ready-to-send short-form update (Slack / email — broader team)**

> *2026-05-26 weekly forecast brief — four-week horizon 05-25 → 06-21.*
>
> - **BOS→PHL LTL** is **+162% wk 06-08 and +148% wk 06-15** (back-to-school on the electronics account, driver confirmed). MCQ overage ~26 loads. **Mini-bid posting by Thursday EOD; close 06-02, award 06-04.**
> - **Drayage LGB→ONT** is **-12%** — frees 28 chassis-days of committed capacity. Releasing to PacWest by EOD today; reallocating to the PHL→ATL reefer overage wk 3.
> - **Parcel WCD outbound** runs **52% above baseline** wk 2–3 — Saturday sort line going on the labor board this week.
> - **Forecast accuracy** is mostly within tolerance; **consumer-electronics SKU family is under-forecasting by 14%** — D. Lin posting a manual override tonight before tomorrow's huddle.
>
> Questions to procurement, dispatch, and WCD ops by Tuesday 09:00 CT huddle.

**Internal notes**

- **Snapshots:** forecast pull 2026-05-22 23:14 CT (planner's model v4.2.1); actuals pull 2026-05-23 23:30 CT (TMS export, 4-wk window); capacity pull 2026-05-23 EOD (routing-guide v2026-Q2 + committed-drayage register); inventory pull 2026-05-23 EOD (WCD + NE DC, on-hand only — in-transit not netted).
- **Assumptions made:**
  - Back-to-school upside on the electronics account is treated as **confirmed driver** based on the customer's 2026-05-20 PO commitment letter and the +52% rolling-4-week trend on the SKU family
  - PHL→ATL reefer +28% is treated as **unconfirmed driver** pending planner-side check-back with the customer; the recommendation does *not* yet propose a routing-guide refresh on the lane
  - Drayage flex on the LGB→ONT lane assumes PacWest accepts the 7-day notice per the 2026 agreement; if PacWest pushes back, the fallback is to absorb the 28 chassis-days as paid-and-unused this cycle and revisit the flex clause in the Q3 contract review
  - MAPE / bias computed at the lane-week grain (consistent with config: `forecast_grain: lane_week`, `planning_horizon_weeks: 4`)
- **Saved to** `outputs/forecast-summary-brief-2026-05-26.md` (if confirmed).
