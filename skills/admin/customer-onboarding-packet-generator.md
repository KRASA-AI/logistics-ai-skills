---
name: "Customer Onboarding Packet Generator"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~2 hrs/onboarding"
version: 1.1
last_eval_score: 8.8
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

## Reference Example

**Input (carrier onboarding intake):**

> We are the 3PL onboarding a new **carrier** ahead of its first tender. Counterparty: **Redhawk Carriers LLC**, DBA Redhawk Logistics; LLC; billing + remit-to 4120 Sweeten Creek Rd, Asheville NC; operating territory Southeast (GA/SC/NC/TN/VA lanes). **MC-998742 / DOT-3214567 / SCAC RDHK.** Equipment: 12 dry van, 4 reefer. Commodities: dry consumer goods + temp-controlled food-grade (no hazmat, no HVT). Typical 30k–42k lb TL. Payment: **factoring via Triumph Business Capital** (NOA provided); carrier offers **quick-pay 1.5% / 2-day** as alternative. Volume: ~8–12 loads/week expected. SAFER pull 2026-06-08: authority **active** (common + contract), MCS-150 updated **2026-02-10**, insurance on file **$1M auto liability / $100K cargo**. CSA BASIC percentiles: Unsafe Driving **42%**, HOS Compliance **38%**, Driver Fitness **11%** (none above the 65% intervention threshold). COI received but **missing the waiver-of-subrogation endorsement**. **Anti-double-brokering addendum not yet signed.** Carrier rep: P. Akande. Our owners: L. Suarez (commercial), J. Park (ops), K. Mahoney (billing), M. Reyes (claims).
>
> Config: COI required endorsements = additional insured + waiver of subrogation + primary & non-contributory + 30-day notice of cancellation; required cargo limit ≥ $100K (food-grade lanes ≥ $150K); default carrier terms Net 30; quick-pay default 1.5% / 2-day; CSA intervention threshold 65%.

**Onboarding Packet — Redhawk Carriers LLC (MC-998742)**

**Executive summary.** Redhawk Carriers LLC (carrier, Southeast dry + reefer, ~8–12 loads/wk) is being onboarded ahead of first tender. Authority is active and CSA scores are clean (all BASICs well below the 65% intervention line). **Two items block the first tender: an unsigned anti-double-brokering addendum and a COI missing the waiver-of-subrogation endorsement.** One item flags for review: the $100K cargo limit clears dry lanes but is **below the $150K food-grade minimum**, so reefer food-grade tenders are gated until the limit is raised or those lanes are excluded. Targeted go-live: first dry-van tender on receipt of the two blockers.

**Compliance disposition**

```
Item                       Disposition  Source / basis
──────────────────────────────────────────────────────────────────────────
Operating authority        PASS         SAFER 2026-06-08 — active, common+contract
MCS-150 currency           PASS         Updated 2026-02-10 (within 24 mo)
CSA BASICs                 PASS         Unsafe 42 / HOS 38 / Driver Fit 11 — all <65
Auto liability             PASS         $1M on file (meets $1M min)
Cargo — dry lanes          PASS         $100K ≥ $100K dry minimum, covers typical value
Cargo — food-grade lanes   FLAG         $100K < $150K food-grade minimum → reefer gated
COI endorsements           BLOCKER      Missing waiver of subrogation (3 of 4 present)
Anti-double-brokering      BLOCKER      Addendum unsigned
Factoring NOA              PASS         Triumph NOA on file; payments route to factor
Hazmat                     N/A          No hazmat authority sought
```

**Packet file list** (folder ready to ZIP)

```
File                                     Purpose                          Owner
──────────────────────────────────────────────────────────────────────────────
01_carrier-agreement_RDHK.pdf            Master carrier agreement         Them (sign)
02_anti-double-brokering-addendum.pdf    No-rebrokering attestation       Them (sign) ⛔
03_W9_Redhawk.pdf                        Tax ID / 1099 setup              Them (provide)
04_COI_Redhawk_endorsements.pdf          Insurance + 4 endorsements       Them (re-issue) ⛔
05_authority-verification_SAFER.pdf      SAFER pull + CSA snapshot        Us (on file)
06_payment-setup_factoring-NOA.pdf       Triumph NOA + quick-pay election Them (confirm)
07_routing-guide_SE-lanes.pdf            Lane/equipment assignment        Us
08_accessorial-schedule.pdf              Standard accessorials (config)   Us
09_contact-matrix.pdf                    Both-side owners + escalation    Us
10_onboarding-summary_1pager.pdf         Snapshot + gap list at top       Us
```

**External cover letter** (to P. Akande, Redhawk)

> P. — Welcome aboard. Attached is the Redhawk onboarding packet. Before we can tender your first load we need two items back: **(1)** the signed anti-double-brokering addendum (file 02), and **(2)** a re-issued COI adding the **waiver-of-subrogation** endorsement — your current certificate has additional-insured, primary & non-contributory, and 30-day-notice, so this is the only one missing (file 04). Separately, your cargo limit of $100K covers our dry-van lanes, but our food-grade reefer lanes require **$150K** — let us know if you'd like to raise the limit to open those lanes, otherwise we'll route you dry-van to start. Please confirm your payment election (Triumph factoring on file, or quick-pay at 1.5% / 2-day) on file 06. Target: first dry-van tender the day those two items land. Reach me or J. Park (ops) with anything. — L. Suarez

**Internal handoff note** (ops / billing / claims)

> **Redhawk Carriers (MC-998742, SCAC RDHK) — cleared to tender dry-van on 2 blockers closing.** Authority active, CSA clean. **Do not tender any food-grade reefer load** until cargo limit raises to $150K (currently $100K) — dry-van only at go-live. Payments route to **Triumph (factoring NOA on file)** — billing (K. Mahoney) flag any direct-pay request as a red flag and confirm against NOA. Ops owner J. Park; claims M. Reyes. Re-verify SAFER + COI at the 6-month mark (2026-12-08).

**Contact matrix & 90-day cadence**

```
Function     Our side      Redhawk      | Cadence
─────────────────────────────────────── | ──────────────────────────────
Commercial   L. Suarez     P. Akande    | Day 1   Kickoff + first-tender
Operations   J. Park       (dispatch)   | Day 7   First-load review
Billing      K. Mahoney    (AP/Triumph) | Day 30  Scorecard baseline
Claims       M. Reyes      (safety)     | Day 60  OTP/OTD trend check
After-hours  ops line      P. Akande    | Day 90  QBR + reefer-limit revisit
```

**Gap list (sorted by severity)**

```
Sev        Item                                          Target close
──────────────────────────────────────────────────────────────────
BLOCKER    Signed anti-double-brokering addendum         Before 1st tender
BLOCKER    COI re-issue w/ waiver of subrogation         Before 1st tender
IMPORTANT  Cargo limit $100K → $150K (food-grade lanes)  Within 30 days
NICE       Quick-pay vs. factoring election confirmed    By Day 7
```

**Internal notes.** SAFER pull 2026-06-08; COI on file dated within term but endorsement-incomplete; CSA snapshot same date. Net-30 carrier terms inferred from config default (no negotiated terms supplied). **DRAFT — two Blockers open; packet ships with the gap list at the top of the cover letter and is not first-tender-ready until both close.** Re-verify date set for 2026-12-08.

---

*Synthetic example — Redhawk Carriers LLC, MC-998742 / DOT-3214567, the Asheville address, P. Akande, and the L. Suarez / J. Park / K. Mahoney / M. Reyes owners are illustrative. Triumph Business Capital is a real factoring company; the NOA reference is synthetic. SAFER, CSA BASICs, MCS-150, and the COI endorsement set are real regulatory primitives; the specific scores and certificate are not from a real carrier.*
