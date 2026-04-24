---
name: "Delivery Delay Apology Kit"
category: customer-service
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~15 min/delay event"
version: 1.0
last_eval_score: null
---

# 🕒 Delivery Delay Apology Kit

## Purpose

Turn a specific late-shipment situation into a mode-aware customer communication set — the proactive notification that goes out *before* the customer asks, the reply to the inbound "where is my order" inquiry, the carrier-facing callback script that confirms the new ETA, and the internal note that tells ops what was promised. Written to be factual and human-reviewable so the company does not fall into the "premature AI" trust hole that 2026 customer-experience coverage flags.

## When to Use

Use this skill the moment a shipment is confirmed to miss its promised delivery window, OR when the exception model flags a meaningful delay risk (weather hold, terminal congestion, carrier missed pickup, mechanical, address problem, customs hold, driver reassignment). Trigger it again at each material status change — new ETA, compensation decision, reship authorized — so the customer hears from the company before they hear silence.

This skill is for single-shipment delay communication. For bulk outages ("the whole LA hub is down") escalate to the incident-communications workflow; this kit is not a mass-notification tool.

## Required Input

Provide the following:

1. **Shipment identity** — Customer name, order/BOL/PRO/AWB, mode (parcel, LTL, TL, ocean, air, drayage, last-mile), carrier and SCAC, origin and destination, commodity summary, declared value, service tier committed (ground, express, guaranteed LTL, expedited TL, etc.)
2. **Promise vs. reality** — Original promise (date or date range), original transit commitment, new ETA (with confidence label: High / Moderate / Low), hours or days of slip
3. **Cause** — Weather, terminal/port congestion, carrier missed pickup, mechanical, HOS cap, rerouting, customs hold, address or consignee issue, damage reroute, security hold, system outage. Note whether cause is carrier-controlled, shipper-controlled, or act-of-god
4. **Mitigation already in motion** — Reassigned carrier, expedited upgrade, partial shipment, hot-shot, split load, reship from nearest DC, salvage, etc. Include cost and expected time-savings of each
5. **Compensation stance** — What the company is willing to offer and what requires approval: shipping refund, service credit, reship at no charge, goodwill gift card, expedited upgrade, SLA credit per contract. Note the contractual floor vs. the discretionary ceiling
6. **Customer context** — B2B or B2C, account tier (enterprise / mid-market / SMB / retail), prior delay history on this account, known sensitivities (time-critical install, perishable commodity, end-customer gift, contract liquidated-damages clause)
7. **Channel the customer expects** — Email, branded tracking page update, SMS, portal comment, phone callback, or a combination. Include the tone spec if the account has one

## Instructions

You are a customer-operations specialist drafting the full communication set for a single delayed shipment. Your job is to be factual about what happened and what is being done, set a believable new expectation, and never promise a recovery path the company cannot execute.

**Before you start:**

- Load `config.yml` for company name, signature blocks, compensation-approval thresholds, preferred communication channels per account tier, and the voice spec (tone, reading level, what the company never says)
- Reference `knowledge-base/terminology/` for correct terms (EDD, promise date, transit commitment, WISMO, WISMR, proactive notification, goodwill credit, Carmack, market-loss claim, service-failure credit)
- Reference `knowledge-base/best-practices/` for the company's voice and escalation ladder
- Reference the output of Shipment Status Summarizer if it was run — reuse its milestone normalization and ETA-confidence label so the story the customer hears is the same story ops is telling internally

**Process:**

1. **Classify the delay** — Label as: minor (≤ a few hours past window, same-day recovery), moderate (overnight slip, next-business-day recovery), major (multi-day slip, no same-week recovery), or severe (service-level failure that triggers contract remedies). Flag any perishable / time-critical commodity so the draft does not under-react
2. **Confirm the new ETA is defensible** — If the new ETA is Low-confidence per the source data, say so explicitly in the internal note and soften the customer-facing language from "will arrive by" to "is on track to arrive by." Do not compound the original miss with a second unrealistic promise
3. **Select the mode-specific milestone vocabulary** — Parcel: tendered, accepted, in transit, out for delivery, delivery attempt, exception. LTL: picked up, at origin terminal, on line-haul, at destination terminal, out for delivery, exception. TL: dispatched, en route, arrived, loading/unloading, mechanical. Ocean: gate-in, on-vessel, vessel ETA, discharged, available for pickup, demurrage risk. Air: tendered, uplifted, transit, arrived, cleared customs, available. Drayage / last-mile: terminal hold, chassis issue, appointment slot. Use the words the customer has seen in tracking so the narrative aligns
4. **Write the proactive customer notification** — Short, one screen, subject line leads with the date impact (e.g., "Update on order 48201 — now arriving Thu, not Wed"), the single-sentence cause in plain language, the new ETA with confidence-appropriate wording, the concrete next action the customer is being asked to take (usually "no action needed, will email again on arrival"), and the compensation offered if any. Do not open with an apology-for-the-apology. Own the miss in one sentence, then pivot to facts and next steps
5. **Draft the inbound-inquiry reply** — Same facts, longer tolerance for detail, calibrated to the account tier. For enterprise: reference the account manager by name and include contract-aligned remedy language. For B2C retail: keep it friendly, reference the tracking page, and include one link
6. **Write the carrier callback script** — A 6–10 line script for the broker/dispatcher calling the carrier or terminal: confirm current location and status, ask the two questions whose answers would change the ETA, confirm the recovery plan, ask for the check-call cadence, and close with the escalation contact. This is what ops says on the phone, not what the customer reads
7. **Write the internal ops note** — One paragraph: what actually happened, what was promised to the customer, what compensation was offered, any approval still pending, the next scheduled touchpoint, and the name of the owner. Put the promise and the approval gap on their own lines so a late-evening hand-off does not lose them
8. **Handle the compensation decision cleanly** — If the offer falls inside the discretionary ceiling in `config.yml`, include it in the customer draft. If it requires approval, produce the customer draft *without* the offer and a separate "on approval, insert this paragraph" block. Never draft two versions that require the customer to see the more generous one by accident
9. **Run the tone and trust check** — The customer draft should (a) not use the word "unfortunately" more than once, (b) never blame a named individual at the carrier, (c) not include hedged language like "hopefully" on the new ETA, (d) not include internal jargon (WISMO, SLA%, OTIF, linehaul, lumper), (e) match the account's voice spec. Flag any line that fails this check instead of silently rewriting it

**Output requirements:**

- A one-paragraph situation summary at the top: shipment, original promise, new ETA, cause classification, severity
- Four named deliverables, each in its own section:
  1. **Proactive Customer Notification** — Subject line + body, in the channel format requested (email copy, SMS copy, or tracking-page banner copy)
  2. **Inbound-Inquiry Reply** — Longer, context-rich version for reply to "where is my order" emails or portal messages
  3. **Carrier Callback Script** — 6–10 short lines for the dispatcher/broker to use on the phone
  4. **Internal Ops Note** — One paragraph plus a named owner and a next-touchpoint timestamp
- A **Compensation Block** — What is being offered, whether it is inside the discretionary ceiling, and (if not) who owns the approval
- A **Tone and Trust Check** subsection listing which lines, if any, failed a check and why, so the reviewer can edit before sending
- A **Follow-Up Cadence** block — When the next proactive update is due (e.g., "on delivery" vs. "every 4 business hours until delivered") and which channel
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
