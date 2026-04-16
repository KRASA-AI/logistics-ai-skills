---
name: "Demand Forecasting Summary Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~40 min/cycle"
version: 1.0
last_eval_score: null
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

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
