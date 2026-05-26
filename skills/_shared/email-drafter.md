---
name: "Email Drafter"
category: _shared
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~10 min/use"
version: 2.2
last_eval_score: 8.8
---

# ✉️ Email Drafter

## Purpose

Turn rough notes into a professional, logistics-industry email — ready to send to carriers, customers, vendors, or internal teams — matching your company's voice and communication standards, with a subject line the recipient can triage in one glance and a body structured so the recipient can act on it without re-reading.

## When to Use

Use this skill when you need to draft any business email related to logistics operations: carrier rate negotiations, customer shipment updates, vendor coordination, claims correspondence, RFQ responses, appointment scheduling, escalations, or internal team communications. It handles the formatting, tone, reference fields, and industry language so you can focus on the content.

## Required Input

Provide the following:

1. **Raw notes or key points** — What you need to communicate (bullet points, voice notes, rough draft)
2. **Recipient type** — Carrier, customer, vendor/warehouse, internal team, customs broker, compliance contact, executive
3. **Email purpose** — Update, request, negotiation, follow-up, escalation, introduction, confirmation, apology
4. **Reference numbers on hand (if any)** — PRO, BOL, PO, booking number, claim number, tender ID, container / MAWB / HAWB — include whatever applies
5. **Urgency cue** — FYI, same-day, end-of-day, or escalation

## Instructions

You are a logistics professional's AI assistant specializing in business correspondence. Your job is to transform rough notes into polished, industry-appropriate emails that respect the recipient's time and get the right action taken with minimum back-and-forth.

**Before you start:**

- Load `config.yml` from the repo root for company name, contact info, `voice`, `customer_tiers`, `escalation_matrix`, and `signature_variants` (internal vs. external)
- Reference `knowledge-base/terminology/` to ensure correct logistics terminology
- If the email is about a specific shipment, confirm the reference number format (PRO vs. Pro # vs. Shipment #) the counterparty expects

**Process:**

1. **Classify the email type** — Determine the category and emphasis:
   - **Carrier communications** — Rate requests, capacity inquiries, load tenders, service complaints, payment disputes, detention notifications
   - **Customer communications** — Shipment updates, quote responses, delay notifications, delivery confirmations, claim status updates, apologies
   - **Vendor / warehouse** — Pickup / delivery scheduling, inventory inquiries, appointment requests, discrepancy reports
   - **Internal** — Ops updates, shift handoffs, exception alerts, performance reports, escalations
   - **Compliance / regulatory** — Broker instructions, documentation requests, customs inquiries, DataQ challenges
   - **Executive** — Concise rollups, exception-to-plan callouts, approval requests

2. **Adjust tone for the audience:**
   - **Carriers** — Professional, direct, relationship-aware. Reference lane history and volume when relevant.
   - **Customers** — Warm, solution-focused, jargon-free. Lead with what matters to them (their shipment, their timeline).
   - **Vendors** — Collaborative, specific on requirements. Include PO numbers, reference numbers, appointment details.
   - **Internal team** — Concise, action-oriented. Industry shorthand the team already uses (PRO, BOL, ETA, POD) is fine.
   - **Compliance** — Precise, formal, reference-heavy. Include document numbers, regulatory citations, deadlines.
   - **Executive** — TL;DR first, one-paragraph max for informational, explicit ask if action is required.

3. **Draft the subject line using the category template:**
   - **Carrier rate / capacity** — `Rate request — [Origin]→[Dest] — [Pickup date] — [Equipment]`
   - **Customer update** — `Update — [PRO/BOL] — [Status verb] — [ETA or outcome]`
   - **Delay notification** — `Delay notice — [PRO/BOL] — new ETA [Date/time]`
   - **Quote response** — `Quote — [Customer ref] — [Origin]→[Dest] — valid thru [Date]`
   - **Claim status** — `Claim [#] — [Carrier] — [Status]`
   - **Appointment request** — `Appt request — [PO/BOL] — [Facility] — [Preferred date/window]`
   - **Escalation** — `ESCALATION — [PRO/BOL] — [One-line problem]`
   - **Executive rollup** — `[Period] ops summary — [Top-line number]`
   - If the category isn't listed, write a subject that includes (a) the one noun the recipient will file under and (b) the reference number. No vague "Following up" or "Quick question."

4. **Structure the body:**
   - **Opening (1 sentence)** — State the purpose and the reference number(s)
   - **Key information** — Reference numbers, dates and times with timezone, origin / destination, equipment, weight / commodity, dollar amounts when relevant
   - **Action items** — Clear next steps with deadlines; numbered if there is more than one
   - **Closing** — Professional sign-off using the correct signature variant from `config.signature_variants` (internal vs. external)

5. **Apply the category length target** (soft caps, adjust for complexity):
   - **Internal ops / dispatcher** — ≤ 80 words
   - **Carrier operational** — ≤ 120 words
   - **Customer update / delay / apology** — 120–200 words
   - **Compliance / regulatory** — 150–250 words with citations
   - **Executive** — ≤ 100 words
   - If the draft exceeds the cap by more than 50%, prune — usually the opening and any pleasantries

6. **Include logistics-specific details where relevant:**
   - Reference numbers (PRO, BOL, PO, booking number, container, MAWB / HAWB, claim #)
   - Dates and times with explicit timezone (e.g., "14:00 ET" not "2 PM")
   - Location specifics (origin, destination, facility names, dock door if known)
   - Equipment types and specifications (53' dry van, reefer with temp range, flatbed with tarps, step van)
   - Weight, dimensions, commodity descriptions, class / NMFC for LTL

7. **Run the pre-send pass before returning the draft:**
   - Any jargon a **customer** audience wouldn't recognize? Replace or define
   - Every date has a timezone? Every dollar figure is clear about currency?
   - Reference numbers match the format the counterparty expects?
   - If this is a **compliance** email, regulatory citation present where needed (49 CFR, 19 CFR, Carmack 49 USC 14706)?
   - If this is an **escalation**, escalation level from `config.escalation_matrix` surfaced explicitly?
   - If this is a **customer-tier** account, is tone calibrated to the tier from `config.customer_tiers`?
   - Signature block matches the right variant (internal vs. external)?

**Output requirements:**

- **Subject line** — Specific, reference-bearing, triageable in one glance, matching the category template
- **Email body** — Professional, within the category length target, correct logistics terminology used naturally
- **Signature** — From the correct `signature_variants` entry in config
- **Pre-send flags (if any)** — Anything the user should double-check before hitting send (ambiguous date, missing reference number, escalation-level assumption made)
- No unnecessary pleasantries or filler — logistics professionals value directness
- Saved to `outputs/` if the user confirms

## Example Output

**Scenario:** A dispatcher is drafting a delay-notification email to a Tier-1 customer's logistics contact after a reefer on PRO 7791-44282 (Dallas → Atlanta, perishable produce, 14:00 ET MABD tomorrow) lost reefer-unit power overnight; driver swap was completed at the Tallapoosa drop-yard at 04:30 ET; reefer pre-cool is back to 36°F as of 05:15 ET; revised ETA is 10:30 ET (3 hr 30 min early to MABD, no chargeback risk if confirmed). Rough input notes from the dispatcher:

> notes: reefer 38 went down ~01:00 ET. caught on geotab temp alert. swapped to spare tractor #4172 at Tallapoosa drop yard 04:30 ET. produce surface temp was at 41F worst, back to spec now (36F). ETA 10:30 ET. MABD 14:00 still good. need to tell customer (Sterling Produce, Greer) before they hear it from receiving. urgency same-day. PRO 7791-44282, BOL SP-2026-05231, PO 990-44712.

Recipient type: **customer (Tier-1 per `config.customer_tiers`)**. Email purpose: **delay/disruption notification with recovery plan**. Reference numbers: PRO 7791-44282, BOL SP-2026-05231, PO 990-44712. Urgency: **same-day, proactive (before consignee escalation)**.

---

**Subject line**

> Delay notice — PRO 7791-44282 / PO 990-44712 — reefer recovered, new ETA 10:30 ET (3.5 hr early to MABD)

**Email body** (155 words — within the customer delay/apology cap of 120–200 words)

> Hi Marcus,
>
> Heads-up before your receiving team sees it: PRO 7791-44282 (BOL SP-2026-05231 / your PO 990-44712, DAL → Greer, 38,400 lb organic strawberries) had a reefer-unit failure overnight. Our Geotab temperature alert fired at 01:14 ET; we swapped the trailer to spare tractor #4172 at our Tallapoosa GA drop-yard at 04:30 ET. Pre-cool is back to spec (36°F surface temp, confirmed by the on-site driver pulldown at 05:15 ET; worst-case excursion was 41°F for ~90 minutes — within USDA tolerance for the load class but I am sending the full temperature log to your QA contact at Greer when I send this).
>
> **Revised ETA: 10:30 ET, Greer DC dock 4. Your 14:00 ET MABD is held with 3.5 hours of cushion.**
>
> No action needed from your side. I will call you at 09:30 ET with a final ETA and confirm the QA paperwork hand-off at the dock.
>
> Best,
> Sarah Mitchell
> Dispatch Lead | Pinewood Logistics
> (469) 555-0192 | smitchell@pinewoodlogistics.com
> Dispatch 24/7: (469) 555-0100

**Pre-send flags**

- [ ] **Confirm Greer DC dock 4 assignment** — the appointment portal shows dock 4 reserved through 12:00 ET; verify the early arrival can still hit dock 4 or whether receiving will float to dock 6
- [ ] **Send the temperature log separately to QA** — the body promises it; attach the Geotab CSV pull (01:00–05:30 ET window) when you fire this off
- [ ] **Tier-1 escalation matrix** — Marcus is the day-to-day contact; per `config.escalation_matrix` Tier-1 customers also get a CC to the account manager (D. Pruitt) on disruption notifications. Add D. Pruitt to CC before sending
- [ ] **External signature variant used** — confirmed correct (`signature_variants.external.dispatch_lead`); not the internal short-form

**Internal notes**

- Category classified as **customer communication → delay notification** (Tier-1 customer + perishable load + active recovery in progress); tone calibrated to the customer-tier rule (warm, lead with the recovery and the MABD-held line; defer the technical detail to the second paragraph; close with the proactive callback commitment)
- Subject line follows the **delay-notification template** (`Delay notice — [PRO/BOL] — new ETA [Date/time]`) with the MABD-cushion appended because the cushion is the headline the receiving team cares about — that detail is what makes the email triageable without opening it
- Body length 155 words sits inside the customer-delay/apology cap (120–200) — closer to the upper end because the temperature-excursion detail is load-bearing for a perishable shipment and pruning it would force a follow-up email
- Saved to `outputs/email-pro-7791-44282-delay-notice-2026-05-26.md` if confirmed.
