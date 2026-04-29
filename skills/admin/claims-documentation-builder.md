---
name: "Claims Documentation Builder"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/claim"
version: 2.1
last_eval_score: 8.6
---

# 📋 Claims Documentation Builder

## Purpose

Assemble a carrier freight-claim package — cover letter, evidence index, claim amount calculation, and supporting documents list — that meets Carmack-standard filing requirements and gives the claims desk every exhibit a carrier or broker needs to accept liability without a round of requests-for-information.

## When to Use

Use this skill when freight arrives damaged, short, or lost, or when a delay triggers a measurable loss and the shipper or 3PL needs to file a claim against the motor carrier, ocean carrier, rail carrier, or freight broker. Trigger it at the start of the claims process (right after POD exceptions are noted) and again at final filing once repair/replacement invoices are in hand. Also useful for preparing a concealed-damage filing inside the 15-day notification window.

This skill produces a claims package for filing; it does not determine liability. Final filing should be reviewed by the claims manager or cargo-insurance adjuster.

## Required Input

Provide the following:

1. **Shipment identifiers** — PRO number, BOL number, carrier name, SCAC, pickup date, delivery date (or attempted-delivery date), origin and destination, commodity description, declared value, freight class, and the consignor's claim number if already assigned
2. **Exception facts** — Type of loss (visible damage, concealed damage, shortage, complete loss, delay), POD notation exactly as written by the driver, date and time damage was first noted, first point-of-origin for the damage if known (carrier terminal, lumper, weather event)
3. **Evidence on hand** — Photos (with EXIF timestamps if available), inspection report, repair estimate or invoice, replacement invoice, salvage offer, commercial invoice showing landed value, packaging notes, temperature recorder data for reefer loads, seal condition at delivery
4. **Claim amount basis** — Repair cost, replacement cost, salvage credit, freight charges to be refunded, additional charges (inspection fee, disposal fee), and any mitigation the consignee performed
5. **Filing route and deadlines** — Which carrier or intermediary will receive the claim, their claims portal or claim-contact email, and the applicable filing window (standard 9-month window from delivery for US domestic motor carriers under Carmack; 1 year under COGSA for ocean; 15-day notification on concealed damage)

## Instructions

You are a logistics claims specialist's AI assistant. Your job is to package the facts and evidence a carrier needs to adjudicate a freight claim, cite them in the format claims desks expect, and flag any gap that could delay or reduce the payout.

**Before you start:**
- Load `config.yml` from the repo root for company name, claims-contact signature block, default declared-value rules, and carrier claim-contact directory
- Reference `knowledge-base/terminology/` for correct terms (Carmack Amendment, OS&D, concealed damage, salvage, mitigation, subrogation, released-value limitation)
- Reference `knowledge-base/regulations/` if applicable (Carmack 49 USC 14706, COGSA, Montreal Convention, Warsaw, Himalaya clause)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Classify the claim** — Label as one of: visible damage, concealed damage, shortage, loss (non-delivery), delay/market-loss, temperature excursion. Concealed-damage filings must show the 15-day notification was met and must explain why the damage was not visible at delivery
2. **Validate the filing window** — Compute days elapsed from delivery (or attempted-delivery) to today against the applicable window. Flag immediately if the claim is close to or past the deadline. If a released-value limitation applies (commodity class, NMFC note, carrier tariff), note the per-pound or per-article cap on recovery
3. **Compute the claim amount** — Lay out the math explicitly: invoice value + inbound freight portion attributable to damaged goods + mitigation/inspection costs − salvage credit − any pre-agreed deductible = claim amount. Show each line with source evidence (invoice #, repair quote #, etc.)
4. **Build the evidence index** — Number each exhibit (Exhibit A, B, C…). For each: what it is, date, who produced it, what it proves. Typical exhibits: BOL with exception noted, POD with exception noted, carrier delivery scans, photos with timestamps, inspection report, repair/replacement invoices, commercial invoice / packing list, temperature recorder read-out, email trail escalating to carrier
5. **Draft the cover letter** — A one-page claim letter to the carrier claims desk using correct Carmack filing language (statement of loss, demand for payment, claim amount, filing date, list of enclosed exhibits, 30-day response expectation). Professional tone, factual, no inflammatory language
6. **Flag evidence gaps** — List anything missing that would let the carrier reduce or deny the claim: no dated photos, no repair invoice yet, POD exception wording too vague, no salvage evidence, no temperature record for reefer. Separate "blocking" gaps from "would strengthen" gaps
7. **Prepare the packaging instructions** — Describe how to assemble and deliver: PDF bundle order (cover letter → evidence index → exhibits in order), carrier portal vs. email submission, subject-line format the carrier expects (usually: Claim – PRO – Consignee – Amount)

**Output requirements:**
- **Claim header block** — Claimant, carrier, PRO, BOL, delivery date, claim type, claim amount, filing window remaining
- **Cover letter** — Ready to PDF and submit, under one page, Carmack-standard language
- **Claim amount calculation table** — Line-itemized with source exhibit reference
- **Evidence index** — Numbered exhibits with descriptions
- **Gap list** — Blocking vs. nice-to-have, each with a suggested action
- **Filing instructions** — Channel, subject line, expected acknowledgment window
- Correct claims terminology throughout; no editorializing about carrier behavior
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — concealed-damage motor carrier claim, dry van, palletized goods).

**Claim header**

| Field | Value |
|---|---|
| Claimant | Acme Logistics LLC |
| Carrier | Midwest Freightways (SCAC: MWFW) |
| PRO / BOL | 7714-882039 / BOL-44820 |
| Pickup → Delivery | 2026-03-12 (Cincinnati, OH) → 2026-03-14 (Dallas, TX) |
| Claim type | Concealed damage |
| Notification date | 2026-03-19 (day 5 of 15) |
| Filing window remaining | 264 of 270 days (Carmack 9-mo) |
| Claim amount | **$8,412.36** |

**Cover letter (one page, ready to PDF)**

> Midwest Freightways — Claims Department
> 2026-03-21
>
> Subject: Claim — PRO 7714-882039 — Acme Logistics — $8,412.36
>
> Acme Logistics LLC hereby files claim against Midwest Freightways under 49 USC §14706 for concealed damage to commodity tendered on BOL 44820 (PRO 7714-882039), picked up 2026-03-12 in Cincinnati, OH and delivered 2026-03-14 in Dallas, TX.
>
> The shipment (12 pallets, 8,640 lb, freight class 70, declared value $42,000) was received without visible exterior damage on 2026-03-14. Concealed damage to the inner cartons was identified during put-away on 2026-03-19 (day 5, within the 15-day concealed-damage notification window). A timestamped photo set, third-party inspection report, and salvage-offer worksheet are enclosed as Exhibits A–G.
>
> Total claim is $8,412.36, calculated on the attached worksheet (invoice value of damaged units $9,210.00 + inbound freight portion $186.36 + inspection fee $250.00 − salvage credit $1,234.00 = $8,412.36). We request acknowledgment within 30 days under §14706(e) and adjudication on the merits.
>
> Carmack response and payment may be sent to claims@acmelogistics.example, attention Claims Manager. We are available for any factual clarification.
>
> [signature block from config.yml]

**Claim amount calculation**

| Line | Source | Amount |
|---|---|---|
| Invoice value of damaged units | Exhibit C — Commercial invoice INV-22871, lines 4–9 | $9,210.00 |
| Inbound freight (prorated to damaged units) | Exhibit B — BOL-44820 freight charges, 14 of 12 pallets prorated | $186.36 |
| Third-party inspection fee | Exhibit F — Cargo-Check inspection report invoice CC-1102 | $250.00 |
| Subtotal — gross loss | | $9,646.36 |
| Salvage credit (offered, accepted) | Exhibit G — Salvage worksheet, buyer Reclaim Goods Co. | ($1,234.00) |
| **Net claim amount** | | **$8,412.36** |

**Evidence index (Exhibits A–G)**

| Ex | Item | Date | Producer | Proves |
|---|---|---|---|---|
| A | BOL-44820 with delivery exception note | 2026-03-14 | Acme dock supervisor | Tender condition + delivery exception language |
| B | POD signed at delivery (no visible damage) | 2026-03-14 | Driver / consignee | Receipt without exterior exception |
| C | Commercial invoice INV-22871 | 2026-02-28 | Acme | Landed value of commodity |
| D | Photo set (14 images, EXIF 2026-03-19, 09:11–09:46 ET) | 2026-03-19 | Acme QA | Concealed damage condition + scope |
| E | Internal exception report ER-2026-0319-04 | 2026-03-19 | Acme QA | Discovery and notification chain |
| F | Cargo-Check third-party inspection report | 2026-03-20 | Cargo-Check Inc. | Independent damage scope and cause-of-loss opinion |
| G | Salvage offer + acceptance | 2026-03-20 | Reclaim Goods Co. | Mitigation by claimant |

**Gap list**

- *Blocking — none.* All Carmack-required elements are present.
- *Would strengthen:* Driver name and tractor # for the delivery — currently missing from the BOL copy on file. Adds shape if liability is later disputed at the carrier-tenure level. *Action:* request from Midwest Freightways dispatch in the cover-letter reply window.
- *Would strengthen:* Email trail showing the exterior-tender condition was photographed at pickup. *Action:* pull from receiving inbox archive; not blocking because the driver POD has no exterior exception.

**Filing instructions**

- Channel: Midwest Freightways claims portal (preferred per carrier directory) — backup email claims@mwfw.example.
- Subject line: `Claim – PRO 7714-882039 – Acme Logistics – $8,412.36`
- Bundle order: cover letter → claim amount worksheet → evidence index → Exhibits A–G in order.
- Expected acknowledgment: 30 days per Carmack; calendar follow-up at day 28 if no response.
