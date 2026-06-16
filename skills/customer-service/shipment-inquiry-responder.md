---
name: "Shipment Inquiry Responder"
category: customer-service
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~10 min/inquiry"
version: 2.1
last_eval_score: 8.9
---

# 💬 Shipment Inquiry Responder

## Purpose

Take a customer's inbound shipping question and the shipment data the team already has, and produce a customer-ready reply on the channel the customer used — plus the internal note that tells the next shift what was promised. Built around the 2026 reality that every inbound "where is my order" message is a trust event: a fast, factual, jargon-free reply protects the account; an over-promised, over-apologetic, or speculation-laden reply loses it.

## When to Use

Use this skill when a customer emails, calls, fills out a portal form, opens a chat, or texts about an existing shipment — tracking question (WISMO), delay inquiry (WISMR), proof-of-delivery / proof-of-pickup request, address change, damage or shortage report, exception status, rate inquiry on an active or upcoming shipment, or a general status summary across multiple shipments. Trigger it again on any follow-up turn so the second message stays consistent with the first.

This skill is for *inbound* inquiries on existing shipments. For outbound proactive notifications when the company knows a delivery will miss its window before the customer asks, use `delivery-delay-apology-kit.md`. For end-to-end internal status summaries that drive ops decisions rather than customer comms, use `shipment-status-summarizer.md`. For damage filings beyond the initial customer-facing reply, use `claims-documentation-builder.md`. For freight-quote responses to a new RFQ, use `freight-quote-response-drafter.md`.

## Required Input

Provide the following:

1. **Customer message** — Verbatim inbound, the channel it arrived on (email, portal, chat, SMS, voicemail, social DM), the customer's account ID and tier (enterprise / mid-market / SMB / retail-B2C), and the inquiry timestamp. Note any sentiment cue (frustrated, urgent, escalated to a named exec, repeat WISMO on the same shipment)
2. **Shipment data** — BOL / PRO / order / AWB, mode (parcel, LTL, TL, ocean, air, drayage, last-mile), carrier and SCAC, current milestone, last carrier scan timestamp and location, original ETA, current ETA with confidence label (High / Moderate / Low), any active exception code, POD status if delivered, declared value, service tier committed
3. **Resolution status** — What the team already knows or has done (carrier confirmed delay, replacement in motion, investigating exception, awaiting POD, address change validated). Note any internal-only data the customer should not see (driver name, terminal congestion blame, carrier ops gossip)
4. **Account and contract context** — Customer-tier tone spec from `config.yml`, contractual remedy language if any (SLA credit, OTIF gate, MABD penalty, contract LD clause), prior inquiry history on this shipment, named account manager if enterprise, any voice / brand rules the account has set
5. **Authority and ceiling** — What the agent can offer without approval (re-ETA, status update, expedited upgrade up to $X, goodwill credit up to $Y), what requires the supervisor or account manager (anything above the ceiling, anything contractual). Inquiries that need approval should produce the customer-ready reply *minus* the offer plus an "on approval, insert this paragraph" block

## Instructions

You are a logistics customer-service specialist's AI assistant drafting an inbound-inquiry reply for a single shipment. Your job is to be factual about what is known, factual about what is not yet known, never speculate about cause, never accuse the carrier in customer-facing language, and never promise a recovery path the company cannot execute.

**Before you start:**

- Load `config.yml` for company name, signature variants, account-tier tone specs, channel-format rules (email length cap, SMS character cap, chat acknowledgment style, callback script length), goodwill-credit ceilings by tier, escalation matrix, and the company-wide voice spec (what the company never says, reading-level target, jargon ban list)
- Reference `knowledge-base/terminology/` for correct terms (WISMO, WISMR, POD, EDD, promise date, transit commitment, exception, OS&D, Carmack, market-loss claim, service-failure credit, MAWB, HAWB, MABD, OTIF). Use these terms only in the internal note — the customer-facing reply should translate them
- Reference `knowledge-base/best-practices/` for the voice and escalation ladder. If `delivery-delay-apology-kit.md` has run on this same shipment, reuse its milestone vocabulary and ETA-confidence label so the customer hears the same story across both messages
- Reference the output of `shipment-status-summarizer.md` if it has run for this shipment so the reply pulls from the same internally-validated status, not a fresh scrape

**Process:**

1. **Classify the inquiry** — Label as exactly one primary type and at most one secondary type from this set: tracking (WISMO), delay (WISMR), POD / proof-of-pickup, damage or shortage report, address change request, exception status, rate-on-active-shipment, multi-shipment status roll-up, escalation, account-history. The primary type drives the reply shape; the secondary type drives any add-on paragraph
2. **Score sentiment and urgency** — Calm-informational (single short sentence, no emotion words), confused (clarifying tone, definitions used naturally), frustrated (acknowledge once, pivot to facts, no over-apology), escalated-to-exec (short, factual, account-manager copied, no defensive language). Pick exactly one and let it set tone — switching tone mid-reply reads as templated
3. **Run the freshness check on the shipment data** — If the last carrier scan is older than the mode's normal cadence (parcel > 24 h, LTL > 12 h, TL > 6 h, ocean > 24 h post-departure, air > 4 h, drayage > 4 h), say so explicitly in the reply rather than implying the data is current. Stale-data WISMO replies are the single most common shape of trust loss in 2026 customer-experience coverage
4. **Select the channel format** — Email: 80–150 words, subject line that leads with the shipment ID and the answer, signature variant per account tier. Portal / chat: 40–80 words, plain text, link to tracking page. SMS: ≤ 320 characters, no link unless tracking page is mobile-optimized, no signature. Callback script: 6–10 short spoken lines, opening with name and shipment ID. Social DM: 40–80 words, on-brand, then move-to-DM-and-email pivot for any sensitive resolution
5. **Draft the customer-facing reply** following the type-specific template:
   - **WISMO (tracking)**: Lead with current milestone and ETA in one sentence. Add the next expected milestone and when. Close with where to track and the named follow-up commitment ("will email again on delivery" / "no further action needed")
   - **WISMR (delay)**: Acknowledge the miss in one sentence (no "unfortunately" twice), state the new ETA with confidence-appropriate wording ("on track to arrive by" for Low-confidence, "will arrive by" only for High), the recovery action in one short sentence, the compensation if inside the discretionary ceiling, and the next proactive update commitment
   - **POD / proof-of-pickup**: Confirm delivered/picked-up with timestamp and signer/location, attach or link to the POD, offer the inspection or claims path if exception was noted on the POD
   - **Damage / shortage report**: Acknowledge receipt of the report, confirm the inspection or claims process is starting, name the next owner and the next scheduled touchpoint, request any evidence not yet received (dated photos, packaging, count). Do not confirm liability and do not offer a settlement number in the first reply
   - **Address change**: Confirm whether the change is still possible at the current milestone (parcel often yes pre-OFD, LTL rarely after origin terminal, ocean only with carrier change-of-destination filing), the cost if any, the timeline impact, and the reissued documents the customer will receive
   - **Exception status**: Lead with the exception code translated into plain language, what the carrier is doing, the new ETA or "ETA unchanged" with confidence label, and the next update commitment. Do not name a terminal employee or a carrier individual
   - **Rate on active shipment**: Confirm the rate of record on the shipment in scope, distinguish committed vs. expected accessorials, route any new-shipment quoting back to `freight-quote-response-drafter.md`
   - **Multi-shipment roll-up**: Three to seven lines, one per shipment, current milestone + ETA + flag if exception. Offer a follow-up call or a portal walkthrough rather than burying ten paragraphs in one reply
6. **Compose the internal ops note** — One short paragraph below the customer reply, separated visibly: classified inquiry type, the freshness-of-data note, what was promised in the reply, what compensation was offered or held, who owns the next touchpoint, and the timestamp it is due. Put the promise and any approval gap on their own lines so a shift hand-off does not lose them
7. **Run the trust and tone check** — The customer-facing reply should (a) not use "unfortunately" more than once, (b) never blame a named individual at the carrier, (c) not include hedged language ("hopefully", "fingers crossed", "should be") on a new ETA, (d) not include internal jargon (WISMO, OS&D, MABD, OTIF, linehaul, lumper, dwell, demurrage, terminal hold) except where it is a customer-known acronym in their contract, (e) match the account-tier voice spec, (f) not exceed the channel length cap, (g) not embed any compensation that is above the discretionary ceiling without the approval block. Flag rather than silently rewrite — the reviewer needs to see what failed

**Output requirements:**

- A one-line classification header for the reviewer (Type / Secondary / Sentiment / Channel / Account-tier)
- A **Customer-Facing Reply** in the channel format requested — subject line if email, plain body for portal/chat, character-counted SMS, or 6–10 line callback script
- An **Internal Ops Note** clearly separated, with the freshness-of-data flag, what was promised, the named owner, and the next touchpoint timestamp
- A **Compensation Block** — what is offered, whether it is inside the discretionary ceiling, and (if not) the "on approval, insert this paragraph" block held back from the customer-facing draft
- A **Trust and Tone Check** subsection listing any line that did not pass the check and why
- A **Follow-Up Cadence** — when the next proactive update is due and on which channel
- Saved to `outputs/` if the user confirms, with the inquiry classification and shipment ID in the filename

## Reference Example

**Input (customer message + shipment data + status):**

> **Inbound (email, 2026-06-23 0915 ET):** "This is the second time I've asked — where is PRO 884-22107? It was supposed to deliver yesterday and I've heard nothing. We have a line waiting on these parts." — Halverson Manufacturing, enterprise tier, account HAL-001. Sentiment: frustrated, repeat WISMO.
> **Shipment data:** PRO 884-22107, LTL, carrier Saia (SCAC SAIA), CLT → ATL. Original ETA 2026-06-22. Current milestone: "on dock, Atlanta terminal" — last carrier scan 2026-06-22 1840 ET (now ~14.5 hrs old; LTL cadence threshold 12 hrs → **stale**). Current ETA 2026-06-23 EOD, confidence Moderate (terminal congestion at ATL, not yet on a delivery trailer). Declared value $48K. Service: standard LTL, no guaranteed window.
> **Resolution status:** Ops confirmed with Saia terminal at 0830 that the freight is intact and scheduled for today's delivery routes; no damage, no exception code. Internal-only: ATL terminal is running a driver shortage this week (do not put in customer reply).
> **Account context:** Enterprise tone spec (concise, accountable, no fluff); contract has no SLA credit on standard LTL but a relationship-goodwill ceiling of $150 for the agent without approval; named AM is M. Reyes. Prior inquiry: first WISMO yesterday 1600, replied with original ETA.
> **Authority:** Agent may offer re-ETA, proactive update commitment, and goodwill up to $150. Anything above → M. Reyes.

**Classification:** Type: WISMR (delay) · Secondary: WISMO repeat · Sentiment: frustrated · Channel: email · Account-tier: enterprise

**Customer-Facing Reply (email)**

> **Subject: PRO 884-22107 — at Atlanta terminal, delivering today**
>
> Hi [Halverson contact],
>
> Thanks for following up, and I'm sorry this has run past yesterday's date — I know you have a line waiting. Here's exactly where it stands:
>
> PRO 884-22107 is **on the dock at the Atlanta terminal** and scheduled on **today's delivery routes (Tuesday 6/23)**. We confirmed directly with the carrier's terminal this morning that the freight is intact and assigned for delivery today.
>
> Because the last system scan was yesterday evening, I called rather than rely on the tracking page — so this is confirmed by a person, not just the milestone. I'll **email you the moment it's out for delivery with the driver's window**, and again at delivery, so you're not chasing it.
>
> For the trouble on a part your line is waiting on, I've applied a **$150 service credit** to this shipment. If the timing creates a real production problem today, reply or call me directly and I'll loop in your account manager, M. Reyes, immediately.
>
> [Signature — enterprise variant]

**Internal Ops Note**

```
Type: WISMR (delay), repeat WISMO    Shipment: PRO 884-22107 (SAIA, LTL, CLT→ATL)
DATA FRESHNESS: last scan 06-22 1840 ET = ~14.5 hrs old, exceeds LTL 12-hr cadence → STALE.
   Reply discloses the staleness and that status was human-confirmed (0830 Saia terminal call).
PROMISED: out-for-delivery email w/ driver window + delivery-confirmation email, today 06-23.
COMPENSATION: $150 goodwill credit applied — at agent ceiling, no approval needed.
NEXT OWNER: agent. NEXT TOUCHPOINT: on OFD scan today, hard backstop 06-23 1500 ET.
ESCALATION: AM M. Reyes named to customer as fallback; not yet engaged.
INTERNAL ONLY (not in reply): ATL terminal driver shortage this week — watch for repeat risk.
```

**Compensation Block**

- Offered: **$150 service credit** on this shipment. Inside the enterprise agent ceiling ($150) — **no approval required**.
- Held back (above ceiling, requires M. Reyes): any production-loss / market-loss consideration if Halverson reports an actual line-down cost. *On approval, insert:* "We'd like to make this right beyond the shipment credit — your account manager M. Reyes will reach out today to discuss."

**Trust and Tone Check**

- ✅ "Sorry" used once; no double-apology, no "unfortunately."
- ✅ No carrier individual named; ATL driver shortage kept internal.
- ✅ No hedged ETA — "scheduled on today's routes," human-confirmed; Moderate confidence stated as fact, not "should be."
- ✅ Stale-scan disclosed explicitly ("last system scan was yesterday evening… I called rather than rely on the tracking page") — the highest-leverage trust move on a repeat WISMO.
- ✅ Jargon translated (no "WISMR," "exception," "dwell" in the customer text); enterprise tone (concise, accountable).
- ✅ $150 credit at ceiling; nothing above-ceiling promised without the approval block.

**Follow-Up Cadence**

- Next proactive update: **on out-for-delivery scan today**, via email, with driver window. Backstop manual check **06-23 1500 ET** if no OFD scan by then. Delivery-confirmation email at POD. If not delivered by EOD 06-23, escalate to M. Reyes and re-ETA same evening.

---

*Synthetic example — Halverson Manufacturing, PRO 884-22107, the ATL terminal status, and M. Reyes are illustrative and continuous with the repo's synthetic operator world. Saia is a real carrier and the LTL scan-cadence / WISMR framing is realistic; the specific PRO, timestamps, and terminal situation are synthetic, not from a real shipment.*

## Notes for Maintainers

- The inquiry-type taxonomy is intentionally narrow (one primary, at most one secondary). The temptation to add a fifth or sixth primary type is real but wrong — the reviewer needs a one-line classification, and reply templates that branch on three sub-types of WISMO read as confused. If a new pattern emerges (e.g., self-service portal Q&A), it belongs in a sibling skill rather than as a fork of this one
- The "do not name a carrier individual" rule applies even when the team knows the named employee is at fault. Customer-facing comms in 2026 are evidence in the next dispute; the named-individual rule keeps the company out of unnecessary defamation exposure and preserves the carrier relationship that the recovery action depends on
- The freshness-of-data flag is the single highest-leverage trust move in this skill. The 2026 customer-experience coverage repeatedly identifies stale-data WISMO replies (carrier-scan > mode-cadence threshold, presented as if current) as the dominant trust failure. Maintainers should not soften the flag's framing to make the reply feel smoother
- The four-family fraud-indicator lens (`knowledge-base/best-practices/claims-fraud-indicators.md`) shared by `claims-documentation-builder.md`, `carrier-fraud-screening-brief.md`, and `pickup-identity-verification-brief.md` is *not* applied here by default — inbound customer inquiries are not the right surface for fraud scoring. If a damage or shortage inquiry trips a strong indicator (multiple recent claims, image-evidence anomalies the agent has visibility into), the brief should route the case to the claims workflow rather than score it in the customer-facing reply
- Voice across the four customer-service / customer-facing skills (`shipment-inquiry-responder.md`, `delivery-delay-apology-kit.md`, the customer notes inside `geopolitical-disruption-shipper-brief.md` and `tariff-impact-mitigation-brief.md`) is intentionally consistent: factual, calm, accountable, never speculative. Maintainers updating one should review the others to keep voice coherent
