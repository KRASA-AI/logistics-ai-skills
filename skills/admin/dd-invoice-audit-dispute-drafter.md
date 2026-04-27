---
name: "Detention & Demurrage Invoice Audit and Dispute Drafter"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/invoice batch"
version: 1.0
last_eval_score: null
---

# 📦 Detention & Demurrage Invoice Audit and Dispute Drafter

## Purpose

Take a batch of ocean-carrier or motor-carrier detention and demurrage (D&D) invoices, audit each line against contract free-time, terminal records, and shipment events, separate the legitimate charges from the disputable ones, and produce the dispute letter the carrier will actually accept. Built around the 2026 reality that container D&D is back to being a margin killer and that recoveries hinge on timestamped evidence rather than goodwill.

## When to Use

Use this skill when D&D invoices arrive from an ocean carrier, motor carrier, or terminal operator and the company needs to audit them before paying, OR when a customer pushes back on a D&D pass-through and the company needs the evidence pack to resolve it. Run it batch-style on a weekly or monthly cadence rather than per-invoice — patterns across a batch (one terminal driving 80% of the demurrage, one consignee driving 60% of the detention) are where the recovery dollars live.

This skill is for **container D&D and chassis detention** — the ocean / drayage / intermodal side of the accessorial fight. For **dock-door detention at the company's own warehouse**, use `dock-scheduling-detention-brief.md` (it operates on the dock side and produces schedule changes, not invoice disputes). For carrier-fraud screening at onboarding or tender, use `carrier-fraud-screening-brief.md`. For the customer-facing communication when a pass-through is being shared, hand the dispute pack into `delivery-delay-apology-kit.md`'s compensation block or into the renegotiation flow.

## Required Input

Provide the following:

1. **Invoice batch** — Each invoice's carrier or terminal, invoice number and date, container or chassis number, BOL or booking reference, charge type (demurrage / detention / per-diem / chassis split / yard storage), free-time claimed, billed-time claimed, daily rate, and total. Note whether the invoice is open, partially paid under protest, or already paid
2. **Contract free-time terms** — The applicable contract or tariff for each carrier: standard free-time (often 4–5 calendar days demurrage at port, 2 days for chassis), tier breakpoints and per-day rates, any negotiated extensions, weekend / holiday treatment, suspension-of-time clauses (e.g., terminal congestion, force majeure), and the OSRA-2022 / FMC reasonableness-rule provisions the carrier is bound by on U.S. ocean lanes
3. **Container event log** — The authoritative timeline per container: vessel ETA, vessel actual arrival, gate-in / discharge, available-for-pickup notification, customs hold start and release, terminal appointment created, gate-out, empty return appointment, empty return actual. Include the source for each event (terminal portal, carrier track-and-trace, drayage company POD, AIS, customer notification)
4. **Operational disruption record** — Documented terminal congestion (terminal closures, dwell-time extensions published by the port authority), labor actions, weather closures, IT outages on the terminal appointment system, customs hold delays, USDA / FDA hold delays. These are the evidence-grade facts that suspend free-time clocks
5. **Internal control records** — When the company or its drayage carrier requested an appointment, what was offered, when the empty was released to the consignee, when the consignee returned the empty. Include the gate receipts and the drayage POs
6. **Customer pass-through stance** — For each shipment generating a charge, the contractual position with the consignee (full pass-through, partial, capped, none) and any prior claim correspondence already on file
7. **Decision authority and recovery thresholds** — Below what dollar threshold a charge is paid without dispute, above what dollar threshold the dispute escalates to a named manager, and the cutoff date by which the carrier requires a written dispute to consider it

## Instructions

You are a logistics finance and operations specialist's AI assistant. Your job is to convert a noisy invoice batch into an audit table that pinpoints the disputable dollars and a dispute draft that uses the carrier's own contract language and timestamps. The voice of the dispute is professional and evidence-led, never accusatory.

**Before you start:**

- Load `config.yml` for company entity, contracts of record per carrier, payment terms, dispute escalation contacts, and the voice spec for carrier and customer correspondence
- Reference `knowledge-base/regulations/` for current FMC rules on detention and demurrage reasonableness (the 2024 final rule's invoice-content requirements: shipper, consignee, container, dates, free-time, accrual basis, billing-party contact, dispute window), the 14-day OSRA-mandated dispute window for ocean carriers on U.S. trade, and any state-level chassis-split or per-diem case law the team is working
- Reference `knowledge-base/terminology/` for correct terms (demurrage vs. detention, chassis split, per-diem, dwell time, last-free-day, gate-out, line-haul vs. pickup, suspension-of-time, force-majeure clause, OSRA reasonableness factors)
- Reference `knowledge-base/best-practices/claims-fraud-indicators.md` if any line in the invoice batch shows pattern signals consistent with billing irregularity (round-number recurrences, charges that exceed contractual caps, billing on dates the terminal was demonstrably closed) — this is not fraud-screening of the carrier, but the same pattern lens helps surface anomalous billing
- If a previous batch's audit lives in `outputs/`, load it to spot recurring offenders (one terminal, one consignee, one drayage carrier driving disproportionate exposure)

**Process:**

1. **Normalize the batch** — Build one row per invoiced line: carrier, container, BOL, charge type, claimed free-time start, claimed free-time end, claimed accrual days, daily rate, total, invoice date, dispute deadline. Reject upfront any invoice that fails the FMC content rule (missing required field, no free-time stated, no accrual basis, no dispute contact) and flag those for an across-the-board procedural rejection — that's a separate, high-leverage dispute on its own
2. **Reconstruct the authoritative timeline per container** — From the event log, build the company's version of the clock: when the container actually became available, when an appointment was actually obtainable (not just when one was theoretically schedulable), when the consignee actually had access, when it was actually returned. Note every divergence from the carrier's claimed dates with the source-of-truth
3. **Apply the contract free-time math line by line** — For each invoice line, compute the company's expected charge under contract: free-time consumed, accrual days at the correct tier, daily rate at the correct tier, weekend / holiday treatment per contract, any suspension-of-time period that should not have accrued (terminal closure, IT outage, customs hold not attributable to the consignee). Compare to billed. Bucket as **Match** (pay), **Contract Math Wrong** (dispute the math), **Wrong Counterparty** (charge belongs to a different party in the chain), **Suspended Period Misapplied** (free-time clock should have stopped), **No Notification** (consignee was not properly notified container was available), **Procedural Defect** (FMC content rule failure), or **Reasonable but Allocable** (legitimate carrier charge, but pass-through to consignee per contract)
4. **Quantify the dispute pack** — Total dollars in scope, dollars expected to recover at high-confidence (Contract Math Wrong, Suspended Period Misapplied, No Notification, Procedural Defect), dollars to negotiate (Reasonable but Allocable mixed-fault), and dollars to pay without dispute. Show the recovery rate the team should expect, given the evidence quality, not a 100% headline
5. **Spot the patterns across the batch** — One terminal generating outsized demurrage, one consignee generating outsized detention, one drayage carrier failing to make appointments inside free-time, one corridor where the contract free-time is structurally too short for the actual port dwell. These are the conversations that change next quarter's costs, not just this month's invoice
6. **Draft the carrier dispute letter** — One letter per carrier (not per invoice), grouping the disputable lines by category. Each disputed line cites: invoice number, container, the specific contract section or FMC rule the dispute rests on, the company's evidence (with source), and the specific dollar adjustment requested. The letter requests a credit memo or revised invoice within the carrier's own dispute-resolution SLA, names the company contact, and closes with the escalation path (FMC charge complaint if the carrier does not respond to a reasonable dispute on a U.S. lane). Voice is professional, factual, and contract-anchored — no accusation, no emotion, no hedge
7. **Draft the customer pass-through note where applicable** — For consignees with full or shared pass-through, a short note summarizing which charges are being passed through, which are being absorbed, and which are in dispute on the customer's behalf. Frame the dispute work as a service the company is doing for the consignee
8. **Produce the internal recovery tracker entry** — Per disputed letter: amount in dispute, expected recovery, dispute-deadline date, owner, next-touchpoint date, and the linkage back to the invoice batch ID so finance can reconcile the credit memos when they arrive
9. **Run the trust and evidence check** — Before finalizing, scan the dispute letter for: (a) any disputed dollar that lacks a cited source-of-truth event, (b) any contract citation that lacks a section reference, (c) any FMC-rule citation that lacks the specific subsection, (d) any blame language directed at a named carrier employee, (e) any suspension-of-time claim without a published terminal advisory or equivalent. Flag failures rather than silently rewrite — a confidently wrong dispute letter weakens the next dispute even when this one wins

**Output requirements:**

- A one-paragraph batch summary at the top: invoices in batch, total billed, total in dispute, expected recovery range, top three patterns
- An **Audit Table** — every invoice line with bucket, expected charge, billed charge, delta, evidence citation
- A **Pattern Findings** block — one paragraph per recurring offender (terminal, consignee, drayage carrier, corridor), with the recommended next step (renegotiate free-time, change drayage assignment, add appointment-process control, escalate to commercial)
- A **Carrier Dispute Letter Set** — one letter per carrier, contract- and FMC-cited, with the disputed-line table embedded
- A **Customer Pass-Through Note Set** — one note per applicable consignee
- An **Internal Recovery Tracker** entry — table-formatted, ready to paste into the finance recovery sheet
- A **Procedural Rejection Letter** if any invoices in the batch failed the FMC content rule — this is a category-level dispute that often clears entire invoices
- A **Trust and Evidence Check** subsection listing any disputed line that did not pass the check, with the gating action (pull the gate receipt, request the terminal advisory, get the appointment system log)
- Saved to `outputs/` if the user confirms, with the batch ID in the filename

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- The skill is intentionally batch-oriented. Per-invoice disputes are a poor use of operator time; the leverage is in the patterns. The 2026 D&D coverage is consistent on this — the tools that generate measurable recovery are the ones that audit and dispute in batches with timestamped evidence, not the ones that auto-reply to single invoices.
- The FMC reasonableness rule and OSRA-2022 dispute window are explicitly cited because U.S. ocean trade is the highest-volume lane the typical operator faces, and the procedural-rejection lever (an invoice missing required content) is the single highest-leverage move when it applies. If the company's lane mix is heavily non-U.S., maintainers should add the equivalent regulatory anchors for the relevant jurisdictions to `knowledge-base/regulations/`.
- Chassis-split and per-diem are folded into the same audit because the operator's clock is the same — the carrier's clock just looks different in the invoice. Splitting these into separate skills would force the operator to maintain two timelines for the same physical container.
- The dispute letter voice is non-negotiable: contract-anchored, evidence-led, no accusation. The 2026 customer-experience coverage on premature AI-generated comms applies equally to carrier-facing comms — a tone-bad dispute letter erodes the next negotiation.
