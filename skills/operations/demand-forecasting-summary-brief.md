---
name: "Demand Forecasting Summary Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~40 min/cycle"
version: 1.2
last_eval_score: 8.9
---

# 📈 Demand Forecasting Summary Brief

## Purpose

Turn a raw demand forecast (volume by lane, SKU, or region) and recent actuals into a short operations brief that planners, carrier-sourcing leads, and warehouse managers can act on: where volume is trending up or down, where the forecast is diverging from reality, where capacity or inventory needs to move, and what the forecast implies for carrier commitments and labor plans over the next 2–8 weeks.

## When to Use

Use this skill for the weekly S&OP or transportation-planning huddle, at the start of each month's capacity commitment cycle, when a seasonal peak is approaching (produce, back-to-school, holiday, returns), when a new customer onboarding adds volume that will shift the baseline, or any time the planning team needs to translate a statistical forecast into on-the-ground decisions that dispatch, yard, and procurement teams understand.

## Required Input

**Only items 1 and 2 are hard requirements.** A forecast and the matching actuals are the irreducible minimum — without them there is no brief. Items 3–5 improve the brief but are **defaulted from config and disclosed, never asked for**, so a planner who has a forecast export and nothing else still gets a usable brief on the first pass.

**Required (the brief cannot run without these):**

1. **Forecast dataset** — Volume (loads, pallets, units, or TEU) by lane, SKU family, DC, or region for the next 4–8 weeks; include the prior forecast if available so drift is visible
2. **Recent actuals** — The last 4–8 weeks of actuals in the same grain, so forecast accuracy (MAPE or bias) can be computed

**Optional — supplied values override config; omitted values are defaulted and disclosed:**

3. **Known demand drivers** — Promotions, new account onboards, product launches, customer contract changes, or planned inventory shifts that are already baked into the forecast. *If omitted:* do not ask. Infer candidate drivers from the actuals (a sustained multi-week step change on one lane or SKU family is a candidate driver) and label every one of them **driver-unconfirmed**, which caps that movement's confidence at *lower — manual review* and turns it into a named planner check-back in the confidence note rather than a recommended move
4. **Capacity context** — Current committed carrier capacity per lane (contracted MCQ, routing-guide primary/secondary), DC throughput limits, and current on-hand inventory at key nodes. *If omitted:* default from `forecast.capacity_register` and `forecast.dc_throughput`. Where a lane has no config entry, report the movement with the capacity implication marked **capacity-unknown** and surface it in the internal notes as the highest-value missing input — never drop the lane from the movements table
5. **Planning objective** — What decision this brief is feeding: weekly carrier allocation, pre-positioning move, peak-season hiring, routing-guide refresh, or an S&OP readout. *If omitted:* default to `forecast.default_objective` and state the assumed objective in the executive summary's first line so a wrong assumption is visible and correctable in one reply

**Ask-only-if-blocking rule:** the only clarifying question worth asking is one whose answer changes the *direction* of a recommended move and has no config default to stand in. Everything else is defaulted, labeled, and delivered. Never hold the brief for an input that has a config default.

## Configuration Reference

- `config.yml` — `forecast.mape_tolerance` and `forecast.bias_tolerance` (the in-tolerance / out-of-tolerance waterline splitting the step-2 accuracy table), `forecast.grain` (lane-week, SKU-week, DC-week — the grain MAPE and bias are computed at), `forecast.planning_horizon_weeks` (columns in the step-3 movements table), `forecast.capacity_register` (per-lane contracted MCQ + routing-guide primary/secondary stack, so a movement that crosses an MCQ ceiling is flagged in step 4 even when the request supplies no capacity context), `forecast.dc_throughput` (per-site daily/weekly ceiling and OT cost step used in the step-4 labor call), `forecast.customer_tiers` (tier → missed-MABD slack, which decides whether an upside lane needs a protective move or can ride), `forecast.spot_benchmark_source` (rate index named when pricing step-6 spot exposure), `forecast.move_owners` (default owner per move type — allocate / pre-position / re-plan labor / reset forecast — pre-filling the step-5 owner column when the request names nobody), `forecast.default_objective` (assumed planning objective when the request omits one), `voice` (short-form tone). An input-supplied value always overrides the matching config default, and every config-sourced value is surfaced in the internal-notes block labeled *config* vs. *supplied*.
- Knowledge base — `knowledge-base/terminology/` (MAPE, bias, MCQ, primary/secondary tender, pre-positioning, safety stock, lane, S&OP)
- Sibling skills — `skills/operations/last-mile-pre-positioning-advisor.md` (consumes this brief's per-depot movements and confidence flags), `skills/operations/carrier-rate-comparison.md` (prices the mini-bid this brief recommends), `skills/operations/backhaul-deadhead-reducer.md` (absorbs the freed capacity on downside lanes)

## Instructions

You are a demand-and-supply planner translating a forecast into the operational moves that need to happen this week and next. Your job is to distill the forecast into a brief that stops being about statistics and starts being about action — while still being honest about uncertainty.

**Before you start:**

- Load the `forecast.*` config-defaults profile named in the Configuration Reference. Every optional input the request omitted is filled from that profile — not asked for — and disclosed in the internal notes labeled *config* vs. *supplied*
- Reference `knowledge-base/terminology/` for correct terms (MAPE, bias, MCQ, primary/secondary tender, pre-positioning, safety stock, lane, S&OP)
- Confirm the audience — operations vs. sales vs. finance — the emphasis differs. If the request names no audience, assume the objective in `forecast.default_objective` implies it (a carrier-allocation objective implies ops + procurement; an S&OP readout implies ops + finance) and state the assumption in the first line of the executive summary

**Process:**

1. **Sanity-check the inputs** — Scan the forecast for obvious anomalies (a 300% week-over-week jump on a single lane with no known driver, negative volumes, missing weeks). Flag anything that looks like a data glitch before interpreting it
2. **Compute accuracy against recent actuals** — Calculate week-over-week MAPE and directional bias (over- or under-forecasting) at `forecast.grain`. Split the table on the `forecast.mape_tolerance` / `forecast.bias_tolerance` waterline so in-tolerance and out-of-tolerance rows are visibly separated. Call out the lanes, SKUs, or regions with the worst accuracy and identify the likely cause (promo not reflected, new customer ramp, seasonality mismodeled) — where no driver was supplied, the cause is a **driver-unconfirmed** hypothesis, not a finding
3. **Identify the top movements** — Rank the top 5–10 volume movements across `forecast.planning_horizon_weeks`: largest upside lanes, largest downside lanes, and lanes crossing a capacity threshold (e.g., forecast exceeds the primary-carrier MCQ in `forecast.capacity_register`)
4. **Map volume to capacity and inventory** — For each top movement, map it against committed capacity and inventory — from the request's capacity context, else from `forecast.capacity_register` and `forecast.dc_throughput` (label it *config*). Flag where a lane is about to exceed primary-carrier MCQ (spot exposure coming), where a DC is approaching a throughput limit (overtime or weekend shift), where a downstream node is at risk of stockout, and where safety stock could be reduced to free working capital. A lane with no capacity entry anywhere is marked **capacity-unknown** and kept in the table, never dropped. Use `forecast.customer_tiers` to decide whether an upside lane on a tier with no missed-MABD slack needs a protective move rather than a watch item
5. **Recommend operational moves** — Produce a ranked set of moves, each with an owner (from the request, else `forecast.move_owners` by move type, labeled *config*) and a timeline:
   - **Allocate capacity** — Raise MCQ with a secondary carrier, post to a mini-bid, or pre-commit a dedicated asset
   - **Pre-position inventory** — Move X pallets from DC A to DC B by date D ahead of the upside
   - **Re-plan labor** — Add a shift or temp labor at a named site for a named week
   - **Reset the forecast** — Refit the model or apply a manual override where accuracy is poor and the driver is known
6. **Quantify the downside and the guardrails** — Note the size of the spot-market exposure (priced against `forecast.spot_benchmark_source`) or stockout risk if no action is taken, and the cost of the proposed action, so the decision-maker can weigh them. A **driver-unconfirmed** movement gets its downside quantified but does *not* get a recommended move — it gets a named check-back instead
7. **Draft the distribution** — Produce a short written brief (≤ 1 page) for the planning huddle and a 3–5 bullet Slack or email update for the broader team. Keep jargon appropriate to the audience; finance and sales will not recognize "MAPE" without context

**Output requirements:**

- A one-paragraph executive summary at the top: forecast horizon, top movement, biggest risk, single recommended action
- Accuracy section showing MAPE and bias for the last window, with 2–3 worst offenders called out
- Top movements ranked in a small table (lane/SKU, next 4 weeks, delta vs. prior forecast, capacity or inventory implication)
- Recommended moves in bullet form, each with owner, timeline, and estimated impact
- A confidence note — which parts of the forecast are high-confidence vs. which deserve a manual override; every **driver-unconfirmed** movement appears here with a named check-back owner
- Ready-to-send short-form update for the broader team in its own section
- An internal-notes block with data snapshot timestamps and any assumptions made, each labeled *config* vs. *supplied*, plus a **defaulted-inputs line** naming every optional input that was defaulted rather than supplied and the single one that would most improve the brief
- Never hold the brief to ask for an optional input that has a config default — default it, label it, deliver it
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
  - MAPE / bias computed at the lane-week grain — *config* (`forecast.grain: lane_week`, `forecast.planning_horizon_weeks: 4`); tolerance waterline 15% MAPE / ±5% bias — *config* (`forecast.mape_tolerance`, `forecast.bias_tolerance`)
- **Config vs. supplied:** forecast dataset, actuals, and the three demand drivers were *supplied*. The MCQ ceilings on BOS→PHL (28/wk) and the PHL→ATL reefer routing-guide stack came from *config* (`forecast.capacity_register`); the WCD sort-line throughput ceiling and the $3.2K OT step came from *config* (`forecast.dc_throughput`); the Tier-1 no-missed-MABD-slack rule on the consumer-electronics account came from *config* (`forecast.customer_tiers`); the $1,090/load spot benchmark came from *config* (`forecast.spot_benchmark_source`); move owners K. Mahoney / R. Singh / J. Ortega / D. Lin were *supplied*, and would otherwise have defaulted from `forecast.move_owners`.
- **Defaulted inputs:** none material this cycle — the planner supplied drivers and capacity context. Had the capacity context been omitted, the BOS→PHL MCQ breach would still have been caught from `forecast.capacity_register`, and the PHL→ATL reefer lane (no config entry) would have been reported **capacity-unknown** rather than dropped. *Highest-value missing input this cycle:* customer confirmation on the PHL→ATL reefer driver — it is the one movement still **driver-unconfirmed** and therefore the one with no recommended move attached.
- **Saved to** `outputs/forecast-summary-brief-2026-05-26.md` (if confirmed).
