---
name: "Spot vs. Contract Rate Negotiation Brief"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/lane"
version: 1.1
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

## Appendix — Negotiation Call Script (added 2026-05-06)

The brief above is the artifact the commercial owner walks into a *prepared* call with. This appendix is the script the owner reads from when the call is happening live — useful when the negotiation runs over voice (carrier-side rate-increase escalation, broker-to-shipper renewal call, shipper-side mini-bid debrief, BAFO round on a multi-lane bid). The 2026 voice-AI category — Parade CoDriver Voice 2.0, Happyrobot, Retell, R1AI, FleetWorks, FTM Cloud, ARK TMS, Numeo and similar — has standardized the *carrier-side* spot-quote-and-basic-negotiation conversation to a degree the human commercial owner needs to step out of when the rate conversation moves above the AI-handled tier into multi-load contract or strategic-account territory. This script is for that human moment, written so the owner can read it cold and still sound prepared.

### When to use the script (not the brief alone)

- The counterparty has scheduled a voice call (vs. a written rate-review exchange)
- The owner needs talking points that survive interruption, pushback, or the counterparty's anchoring move
- The conversation is over the AI-handled tier (multi-lane, contract, or strategic-account level — not a single-load spot quote that the voice-AI tier resolves on its own)
- A written exchange has already happened and the call is the close-out, the BAFO round, or the contested point that did not resolve in writing

### Script structure (eight movements)

1. **Greeting and frame** *(one sentence, ~10 seconds)*. Name, company, lane (or lanes) under discussion, the math the brief produced, and the time the owner is asking for. Example: "Thanks for the call — I want to walk through the LAX–DFW dry-van contract, the 30-day spot read, and where we think the rate should land. About fifteen minutes."
2. **Anchor on the math** *(two sentences)*. State the contract rate, state the 30-day spot read, state the delta in $/load and annualized $/lane. Don't editorialize. Example: "Contract is $2.18/mi all-in. The 30-day DAT read on this lane is $1.97/mi. That's $0.21 per mile, $462 per load, roughly $480,000 annualized at our committed volume."
3. **Frame the trajectory** *(one sentence)*. The 7/30/90-day trajectory and what the brief says about whether the gap is widening, stable, or narrowing. Example: "The 7-day is $1.94 and the 90-day is $2.05, so the gap is widening, not a one-week dip."
4. **Concede the counterparty's likely line** *(one sentence)*. Pre-empt the rebuttal. Example: "I know the natural pushback is service has been clean — and it has. Tender acceptance is 96%, on-time delivery is 98%. We're not raising service. We're raising rate."
5. **Reframe and ask** *(two sentences)*. The recommended posture from the brief, with the specific number. Example: "What we'd like is a $0.15 per mile reduction effective the first of next month — that closes most of the gap and leaves the relationship's service premium in place. We'd hold the rate at the new number through the next contract cycle."
6. **Hold the silence** *(no script — leave it open)*. After the ask, the owner stops. The next person to talk takes the rate position. The 2026 voice-AI category has trained carrier-side dispatchers to expect a follow-up sentence from the buyer; not getting one is the script's most reliable hold-the-floor move.
7. **Handle the three most common moves** *(one branch each)*. Pre-written for the owner to read from:
   - **Counter-anchor** ("we can't go below $2.10"): "I appreciate that. Given where the 30-day spot is and where the trajectory is going, $2.10 leaves us $0.13 above market on a lane our internal benchmark already flagged as exposed. Where can you go in the $1.95–$2.00 range with the rest of the contract held flat?"
   - **Service-equity defense** ("you're getting premium service for premium pricing"): "Right — and the rate we're proposing still pays a service premium. The current contract pays a service premium *plus* a market premium. We're asking to remove the second one."
   - **Volume reciprocity ask** ("can you commit more volume at the lower rate?"): "Possibly. What we can commit is the current-year volume contractually and a first-call position on the next-quarter peak adds. We're not in a position to expand the MCQ this cycle."
8. **Close on a date** *(one sentence)*. Whatever the call lands on, the close is a date and an artifact. Example: "Let's circle back Thursday. I'll send a one-pager confirming whatever we land on today; you bring back internal sign-off." Hand-off the artifact in writing within the same business day so the verbal commitment doesn't drift in either party's memory.

### Three guardrails that keep the call honest

- **Anchor on the brief's math, not the call's anchor.** The counterparty will frequently anchor at a number that ignores the brief's 30-day spot read. The owner's job is to keep returning to the brief's number and the brief's window — not to negotiate against the counterparty's anchor.
- **Concede service to keep the rate move alive.** Rate conversations on a clean-service-history lane lose when the buyer-side framing drifts into a service complaint. The script's move 4 is the structural concede that reserves the floor for the rate move.
- **Hold the silence after the ask.** The strongest move in the script is the unscripted moment after move 5. Voice-AI trained dispatchers expect a follow-up justification sentence from the buyer; the human owner who can hold the floor for five-to-ten seconds without filling it is the move the AI tier cannot replicate.

### Voice-tone notes

Calm, procedural, declarative. No adjectives that overpromise ("phenomenal partner", "great relationship") and no language that reads as adversarial ("you've been overcharging us", "the market has crushed your rate"). The script is the artifact for an *honest* conversation about a real number gap; tone that drifts in either direction undermines the call's outcome.

### Internal-notes block (recorded after the call)

- Date, time, duration, attendees on each side
- The number the call landed on (if any) and the artifact promise (date and channel)
- The counterparty's stated posture for the next contract cycle (extend / open-bid / move-volume)
- Any commitment outside the rate (volume, accessorial change, service-level change) that needs to flow into the company's contract record
- The next-trigger date (when the artifact is due, when the renewal cycle opens, when the next mini-bid runs)

This appendix is the *call script* layer that the brief above did not previously address. The brief produces the math and the written posture; this appendix produces the live-conversation handle. Use both together when a call is on the calendar; use the brief alone when the cycle is written-only.
