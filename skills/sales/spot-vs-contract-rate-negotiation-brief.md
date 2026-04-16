---
name: "Spot vs. Contract Rate Negotiation Brief"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/lane"
version: 1.0
last_eval_score: null
---

# 💸 Spot vs. Contract Rate Negotiation Brief

## Purpose

Produce a negotiation-ready brief for a single lane (or a short list of lanes) that compares the current contract rate against the live spot market, quantifies the gap in dollars per load and per quarter, and recommends a renegotiation posture — hold, open for rebid, file a rate-review letter, or move volume — with ready-to-send talking points for both the shipper-side and carrier-side conversation.

## When to Use

Use this skill in advance of a contract renewal, a mid-cycle rate-review request, an RFP bid response, a lane-by-lane routing-guide refresh, or whenever a carrier escalates a "we need a rate increase" email on a committed lane. It is also the right tool when a shipper notices that its contracted lane rate has drifted far from the spot market in either direction and wants a fact-based response before its next QBR.

## Required Input

Provide the following:

1. **Lane details** — Origin city/state, destination city/state, equipment type (dry van, reefer, flatbed, intermodal), typical weight and commodity, annual or quarterly volume, and contract start/end dates
2. **Contract rate on file** — All-in linehaul rate, fuel-surcharge method (flat, indexed to DOE, pass-through), accessorial schedule, and any minimums or MCQ commitments
3. **Current market indicators** — DAT RateView, Greenscreens, SONAR, Truckstop, or internal benchmark averages for the lane over the last 7, 30, and 90 days; load-to-truck ratio if available
4. **Service history** — Tender acceptance %, on-time pickup/delivery %, claims or detention incidents on the lane over the last 90 days
5. **Negotiation posture** — Role (shipper, broker, or carrier), the counterparty, the ask on the table (if any), and any hard constraints (e.g., DEI routing guide requirements, a volume commitment that cannot be unwound mid-quarter)

## Instructions

You are a logistics pricing analyst preparing a negotiation brief for a rate conversation. Your job is to anchor the discussion in math, not opinion, and to produce a one-page document the commercial owner can walk into a call with.

**Before you start:**

- Load `config.yml` from the repo root for default margin floors, fuel-surcharge table, and negotiation voice guidance
- Reference `knowledge-base/terminology/` for correct terms (linehaul, all-in RPM, MCQ, rate-review letter, primary/secondary tender, routing guide, DAT, Greenscreens)
- Confirm whether the brief is written for a shipper, broker, or carrier audience — the posture and language differ

**Process:**

1. **Normalize the comparison** — Convert the contract rate and each market indicator to an apples-to-apples all-in RPM (linehaul + fuel, excluding accessorials unless both sides include them). Show the arithmetic. If the contract uses an indexed fuel surcharge, compute the current-week fuel-adjusted rate before comparing
2. **Quantify the gap** — Report the delta in $/load and $/mile and annualize it across the lane's volume. Label the direction clearly: "contract $0.18/mi above 30-day spot" or "spot has moved $0.12/mi above contract over the last 60 days"
3. **Frame the market trajectory** — Compare the 7-day, 30-day, and 90-day indicators to identify whether the gap is widening, stable, or narrowing. Note any seasonality, capacity shocks (weather, port congestion, produce season), or tender-rejection-rate signals that would predict continued drift
4. **Evaluate the non-rate variables** — Weigh service history on this lane: if tender acceptance is strong and on-time performance is clean, the rate has earned its premium; if the lane has persistent exceptions, the premium is harder to defend. Flag any customer-priority overrides or strategic account considerations
5. **Recommend a posture** — Choose one primary recommendation and a fallback:
   - **Hold** — Gap is within tolerance; defer the conversation to renewal
   - **Open for rebid / mini-bid** — Gap is material and service is average; run a short mini-bid rather than renegotiate one-to-one
   - **File a rate-review letter** — Carrier-side: formal rate adjustment letter citing market indicators, cost drivers, and continued service commitment
   - **Move volume** — Shift a portion of loads to a secondary carrier until the primary recloses the gap; include volume % and timeline
   - **Counter with a specific number** — Propose a precise rate adjustment supported by the math in the brief
6. **Draft the talking points** — Produce 4–6 bullets the commercial owner can use live, each anchored to a number from the brief. Include one line that concedes what the counterparty is likely to say, and one line that reframes it
7. **Draft the written message** — Produce the outbound email or rate-review letter that supports the recommendation. Keep it factual, not adversarial; cite the market indicators by source and window; end with a clear ask and a proposed date to close the loop

**Output requirements:**

- A one-paragraph executive summary at the top: lane, current vs. market, recommended posture, annualized dollar impact
- The normalized-rate comparison shown as a small table (contract, 7-day, 30-day, 90-day, delta)
- Posture recommendation with rationale in 2–3 sentences
- Talking points in bullet form, each tied to a number
- Ready-to-send email or letter in its own section
- An internal notes section capturing assumptions (fuel table used, indicator sources, volume basis)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
