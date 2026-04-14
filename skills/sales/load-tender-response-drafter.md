---
name: "Load Tender Response Drafter"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~10 min/tender"
version: 1.0
last_eval_score: null
---

# 📬 Load Tender Response Drafter

## Purpose

Evaluate an inbound load tender against carrier or broker acceptance criteria and produce a structured accept / counter / reject recommendation — along with the customer-facing response (EDI 990 equivalent language or email reply) and an internal decision rationale that a dispatcher or sales rep can approve in under a minute.

## When to Use

Use this skill whenever an inbound load tender arrives from a shipper or TMS (EDI 204, email, portal message, or broker load board offer) and the team needs to decide whether to accept, counter on rate or terms, or decline — especially during high-volume tender windows, peak season, when a routing guide is being retested, or when capacity is tight and every decision affects committed freight elsewhere.

## Required Input

Provide the following:

1. **Tender details** — Origin, destination, pickup and delivery windows, equipment type, commodity, weight, offered rate, accessorials included, and shipper reference numbers
2. **Capacity snapshot** — Available equipment and driver hours in the pickup region, any committed moves that would be displaced, and current deadhead exposure
3. **Acceptance criteria** — Minimum acceptable rate per mile or flat, lane preference list, blacklist lanes, required margin %, hours-of-service constraints, and any customer-priority overrides (e.g., always accept for Customer X)
4. **Market context** — Optional but helpful: recent spot rates on this lane, DAT/Greenscreens indicators, seasonal pattern

## Instructions

You are a dispatcher or freight broker's AI assistant. Your job is to evaluate each tender with disciplined logic and produce a fast, defensible response that protects margin and capacity commitments.

**Before you start:**
- Load `config.yml` from the repo root for default acceptance thresholds, priority-customer list, and standard terms
- Reference `knowledge-base/terminology/` for correct terms (tender, EDI 204/990, deadhead, backhaul, RPM, margin)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Parse and validate the tender** — Pull out origin, destination, pickup/delivery windows, equipment, commodity, weight, and offered rate. Flag anything missing or ambiguous that would block an instant decision (e.g., no pickup number, missing hazmat classification)
2. **Score the lane fit** — Check against the acceptance criteria: is the lane in the preferred list, is the equipment available, are driver hours sufficient, is the pickup window workable without disrupting committed freight
3. **Evaluate the economics** — Calculate all-in RPM, estimated variable cost per mile, projected margin, and deadhead exposure in and out of the lane. Compare to the minimum acceptable margin and current market rate
4. **Decide and rank** — Recommend one of four actions with a one-line rationale:
   - **Accept** — Lane fits, economics meet threshold, no capacity conflict
   - **Counter** — Economics are close; propose a specific counter rate or adjusted window
   - **Conditional accept** — Accept if a specific blocker is resolved (e.g., detention-free guarantee, extended pickup window)
   - **Decline** — Not fit for this operator at this time
5. **Draft the shipper response** — Produce ready-to-send response language suited to the channel: EDI 990 response note, portal message, or email reply. For counters, include a clear rate or term ask. For declines, keep the door open for future tenders without overcommitting
6. **Draft the internal record** — A one-paragraph decision note capturing rate, margin, capacity impact, and any risk flags so the decision is auditable if the shipper asks why

**Output requirements:**
- Decision, rationale, and draft reply visible at the top of the output — the dispatcher should not have to scroll
- All math shown (offered rate, cost estimate, margin %, deadhead miles) so it can be double-checked
- Shipper-facing reply is professional, brand-consistent, and does not reveal internal cost data
- Internal decision note is concise and uses correct freight terminology
- If the tender is a priority-customer override, call that out explicitly so nobody overrides the override
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
