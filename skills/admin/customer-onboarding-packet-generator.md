---
name: "Customer Onboarding Packet Generator"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~2 hrs/onboarding"
version: 1.0
last_eval_score: null
---

# 🧾 Customer Onboarding Packet Generator

## Purpose

Turn a new-shipper or new-carrier onboarding intake into a complete, consistent packet — credit application, carrier or shipper setup form, W-9/COI/authority verification block, contact matrix, routing guide, EDI or API spec, accessorial schedule, and a first-90-day cadence plan — so the account goes from signed MSA to first load moving without the usual six-email chase for missing documents. Outputs a packaged ZIP-ready folder structure, a one-page onboarding summary, and a gap list of anything still missing.

## When to Use

Use this skill immediately after a signed MSA with a new shipper, after a carrier qualifies through the vetting process and needs a packet before the first tender, when a regional manager asks for a "standard kit" to send to a prospect that has agreed to try a test lane, when refreshing an existing customer's documents at contract renewal (packet hygiene), or any time the commercial team is about to hand a new account over to operations and wants one artifact that captures everything operations, billing, and customer service need.

## Required Input

Provide the following:

1. **Account type and role** — Shipper, broker, or direct carrier; whether this onboarding is from our side (we are the broker/3PL) or their side (we are the carrier onboarding with a new shipper)
2. **Counterparty basics** — Legal name, DBA, billing address, remit-to address, primary operating locations, tax ID status (domestic W-9 vs. foreign W-8), MC/DOT number if applicable, SCAC, and corporate structure (sole prop / LLC / C-corp / S-corp)
3. **Commercial terms** — Payment terms (Net X, factoring, quick-pay), credit line requested or offered, fuel-surcharge method, accessorial pricing, volume commitments or MCQ, and effective dates
4. **Operational profile** — Lanes, equipment types, commodities (flag reefer, hazmat, food-grade, HVT, pharma, temp-controlled), typical weight range, appointment vs. FCFS, expected tender volume, and service-level expectations
5. **Integration posture** — EDI (204/214/210/990/997), API, TMS portal, or email-only; required milestone events; tracking cadence; invoicing method (EDI 210, portal, email-PDF)
6. **Required compliance documents** — W-9/W-8, COI with required endorsements and limits, carrier authority (MC/DOT + MCS-150 age), CA# / CARB / FDA / TSA / CTPAT / SmartWay as applicable, hazmat endorsement and training, drug-and-alcohol program attestation, anti-double-brokering addendum
7. **Internal owners** — Named owners for commercial, ops, billing, claims, and customer service on our side — the handoff matrix the packet ships with

## Instructions

You are an onboarding coordinator producing a clean, self-contained packet that a new counterparty can complete in one sitting and that operations, billing, and customer service can pick up without asking the commercial team a single clarifying question.

**Before you start:**

- Load `config.yml` for credit-line thresholds, default payment terms, required COI endorsements and limits, and the standard accessorial schedule
- Reference `knowledge-base/regulations/` for current MC/DOT authority verification steps (FMCSA SAFER, CSA BASIC scores), CARB registration, anti-double-brokering clauses, and hazmat BOL requirements
- Reference `knowledge-base/terminology/` for correct terms (COI, additional insured, waiver of subrogation, MCS-150, SAFER, factoring, quick-pay, anti-double-brokering, SCAC, CTPAT, SmartWay)
- Confirm whether the packet is being prepared for a shipper or a carrier — the form set differs and the compliance emphasis flips (credit risk vs. operational risk)

**Process:**

1. **Classify the counterparty and select the form set** — Based on account type and role, pull the right bundle: shipper packet (credit app + MSA references + EDI/API spec + routing guide + billing instructions), carrier packet (W-9, COI, authority verification, carrier agreement + anti-double-brokering addendum + payment method form + factoring NOA handling), or broker packet (both plus broker authority and bond proof)
2. **Verify authority and compliance evidence** — For carriers: check MC/DOT active status, operating authority type (common/contract/household goods), MCS-150 update recency, CSA Unsafe Driving / HOS Compliance / Driver Fitness BASIC percentiles against `config.yml` thresholds, insurance on file on SAFER. For hazmat carriers: verify HM-232 certification, hazmat endorsement by driver population. Flag any items that would block the first tender
3. **Build the credit / payment block** — For shippers: pull requested credit line, determine credit-review depth (≤ $25k waterline vs. > $25k full review), request D&B report or trade references, recommend approval posture. For carriers: capture payment method (ACH, check, factoring), set quick-pay rate if offered, collect NOA acknowledgment if factoring is present, set default Net terms
4. **Build the COI / insurance block** — List required coverages (auto liability, general liability, cargo, environmental if applicable, umbrella) with required limits and required endorsements (additional insured, waiver of subrogation, primary and non-contributory, 30-day notice of cancellation). For shippers: confirm they are named as required. For carriers: confirm our entity is named correctly and endorsements are present
5. **Build the operational and integration block** — Populate the routing guide (primary, secondary, tertiary by lane and equipment), tender method (EDI 204, portal, email), milestone spec (214 events expected, interval of check-calls if EDI is absent), appointment rules per origin/destination, invoicing method, and dispute / claims path
6. **Build the contact matrix and 90-day cadence** — Named owners and escalation path on both sides for commercial, ops, billing, claims, after-hours. First-30-day cadence (kickoff, first-load review, weekly check), first-60-day (scorecard baseline), first-90-day (QBR).
7. **Assemble the packet and run the gap check** — Produce the file-list and a one-page onboarding summary (counterparty snapshot, lanes, terms, compliance disposition, outstanding items). Run a gap check across every required input and flag anything missing by severity: Blocker (cannot run a load until resolved), Important (must close within 30 days), Nice-to-have (close by QBR). Put the gap list at the top of the cover letter
8. **Draft the cover letter and the internal handoff note** — External cover letter: one paragraph of context, a numbered list of what's inside and what's required back, a named point of contact with a response deadline. Internal handoff note: the one-pager for ops / billing / customer service with the relevant flags, owners, and 90-day cadence

**Output requirements:**

- A one-paragraph executive summary: counterparty, role, start date, commercial scope, headline flags
- A compliance disposition block: Pass / Flag / Blocker for each of authority, insurance, credit, anti-double-brokering, hazmat/food-grade as applicable, with source (SAFER pull date, COI expiry, credit-report date) cited
- The file list with a one-line purpose per file and an owner (us vs. them) for each
- The external cover letter and the internal handoff note as separate sections
- The contact matrix and 90-day cadence as a small table
- A numbered gap list sorted by severity with target close date
- An internal-notes block with source-document timestamps, any derivation assumptions (e.g., credit line inferred from pending MSA), and a DRAFT flag if any Blocker remains open
- Saved to `outputs/` as a folder structure ready to ZIP, if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
