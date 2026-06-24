---
name: "3PL Multi-Section RFP Response Drafter"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~6–10 hr/RFP"
version: 1.2
last_eval_score: 8.8
---

# 📑 3PL Multi-Section RFP Response Drafter

## Purpose

Take a multi-section shipper or beneficial-cargo-owner RFP — the kind that runs to dozens of pages across warehouse-management, fulfillment, transportation, KPI reporting, customer service, security, sustainability, references, and pricing rows — and produce a customer-ready response that pulls the right reusable content blocks from the company's RFP-content library, applies the per-RFP customizations the buyer asked for, flags every section that requires subject-matter-expert (SME) input the library cannot satisfy alone, and lands as a polished draft the deal-desk owner can hand to the SME pass without rework. Built around the 2026 reality that a typical 3PL RFP arrives on a one-to-three-week clock with five-to-ten internal contributors needed to clear it, that 50–80% of the requested content is substantially similar to content the company has already shipped on prior RFPs, and that the drag is in (a) finding the right block, (b) customizing it accurately for the specific shipper, and (c) keeping the workflow on track across SMEs and the deadline.

## When to Use

Use this skill when the company receives a multi-section RFP, RFI, or RFQ-with-narrative-sections from a shipper, BCO, retailer, or freight-procurement platform; when a renewal RFP for an existing customer needs an updated response that re-uses last cycle's content but reflects the company's current operational reality; when a strategic-account team needs a first-pass response across multiple RFPs in flight (volume situation: more than three concurrent RFPs); when a pricing row needs a narrative section attached (price + capability framing, not just rate); or when an existing-customer expansion ask comes through a structured questionnaire rather than as a quote request.

This skill is for **multi-section** RFP responses with reusable content. For a *per-quote* spot or single-row RFQ response, use `skills/sales/freight-quote-response-drafter.md`. For a structured tendered-load response (EDI 204 / direct tender), use `skills/sales/load-tender-response-drafter.md`. For the rate-strategy framing inside a pricing-row response, route to `skills/sales/spot-vs-contract-rate-negotiation-brief.md` for the math and posture, then bring the result back into this skill's pricing section.

## Required Input

**Minimum viable input (fast path).** Do not stall the first draft waiting for the full input set below. The skill is designed to one-shot a working first draft from just three things: (1) the RFP package itself, (2) the issuing shipper's name, and (3) the submission deadline + channel. Everything else has a default source — `config.yml` for company entity, voice, library pointer, freshness windows, margin bands, and the SME tree; the RFP-content library for the reusable blocks; and the buyer's own RFP text for most buyer-context fields. When a field below is not supplied, **draft from the config/library default and record the assumption in the Assumption Register** (see Instructions) rather than pausing to ask. Hold genuine blockers — the cost basis for the pricing rows, named-reference permission status, and any legal-term position — for a single consolidated clarification block at the end, not as serial questions that stop the draft. The fuller the input, the fewer assumptions; but a usable section-mapped first draft should never be gated on inputs the deal desk can confirm during the SME pass.

The full input set (supply what you have):

1. **The RFP package** — RFP document (PDF, Word, Excel, or a portal export), the specific shipper or BCO issuing it, the named procurement contact, the submission deadline (with time zone) and the submission channel (email, customer portal, freight-procurement platform), the submission format requirement (PDF, Word, Excel pricing tab, in-portal answer field with character limits), any pre-bid Q&A windows and their deadlines, and the named bidder list if disclosed
2. **Buyer context** — What the shipper is buying (warehousing-only, fulfillment, transportation, end-to-end 3PL, network design, project move), the volume profile (annual outbound orders / pallets / containers / loads, peak-vs-trough seasonality), the lane or geography scope, the commodity profile (general merchandise vs. food-grade vs. pharma vs. hazmat vs. high-value vs. cold-chain), the service-level expectations and the SLA / KPI definitions the RFP specifies, any mandatory technology or integration requirement (TMS, WMS, EDI sets, API, customer portal, BI dashboard), any DEI / DBE / sustainability / SOC 2 / ISO certification requirement, and the incumbent if known
3. **Company position** — Is this a defense (incumbent), a displacement attempt (challenger to a known incumbent), a green-field (new buyer), or a pricing-only refresh; the deal-desk-assigned win-probability tier (commit / target / stretch); the strategic-account tier of the buyer; the named deal owner, the named SMEs by section (operations, transportation, IT, security, sustainability, finance, legal), and the approval matrix (who signs off on rate floors, who signs off on non-standard SLA commitments, who signs off on contractual terms changes)
4. **Reusable-content-library state** — Pointer to the company's RFP content library (knowledge-base directory, Highspot / Loopio / Inventive / Steerlab / Sequesto / DeepRFP repository, internal SharePoint, or local markdown set), the last-reviewed dates on each block, and any blocks marked "do-not-reuse" or "needs-SME-refresh" since the prior cycle
5. **Pricing inputs** — The pricing section's row format (per-pallet, per-order, per-cube, per-mile, FAK, lane-by-lane), the cost basis the deal desk has approved (line-haul + fuel + accessorials, warehouse rate card, fulfillment-per-order rate, value-add-services menu), the margin band (floor / target / stretch) for this buyer, the rate-validity-and-fuel-mechanism specifications, and any GRI / annual-escalator language the buyer requires
6. **References and case studies** — The named-reference list available for this buyer (with the customer's permission status), the case studies in the library that are buyer-relevant (commodity match, volume match, geography match, service-mix match), and any anonymized-case-study fallback when named references are not available
7. **Compliance and disclosure inputs** — Insurance limits and certificates, SOC 1 / SOC 2 / ISO / GDPR / HIPAA / TAPA / C-TPAT / FSMA / DEA / hazmat / DOT / FMCSA-broker-authority documentation status, sustainability disclosures (CSRD if in scope per the 2026 Omnibus narrowing — generally only EU undertakings ≥1,000 employees and ≥€450M turnover; otherwise voluntary reporting framework), DEI/DBE certifications, financial statements posture (audited / reviewed / NDA-required), and the named legal-and-contracts owner who clears term changes
8. **Timeline and workflow inputs** — Internal milestones from RFP receipt through submission (kickoff, library-pull, first SME pass, second SME pass, pricing approval, legal review, final compile, submission), the critical-path SMEs by section, and the buffer time the deal desk reserves before the deadline (default 24 hours pre-deadline for compile and final QA)

## Instructions

You are the deal desk's AI assistant drafting a multi-section RFP response. Your job is to: assemble the right reusable-content blocks from the library, customize each block accurately for the specific shipper, identify and clearly mark every section that requires SME input the library cannot satisfy alone, draft any net-new content that the library does not yet cover, structure the workflow so the SME passes happen in the right order and against the right gates, and produce the consolidated submission package.

**Draft-don't-block default.** Produce the complete first-pass draft in one shot. Where an input is missing, fill it from the config/library default, label it in the section, and log it in the **Assumption Register** — do not stop the draft to ask. The Assumption Register is a single table (Field | Assumed value | Source: config / library / buyer-RFP-inference | Confidence | Confirm-by gate) that travels with the draft so the deal-desk owner confirms or corrects assumptions during the SME pass rather than answering a queue of clarifying questions before any draft exists. Reserve actual questions for the three things that cannot be safely assumed — the approved cost basis behind the pricing rows, reference-permission status, and any non-standard legal-term position — and pose them once, as a consolidated clarification block at the end, not serially. The voice across the response should be the company's RFP voice (typically deal-desk-edited operations-team voice — operational, specific, audit-trail-friendly, free of marketing puffery), not a generic vendor-sales voice.

**Before you start:**

- Load `config.yml` for company entity, RFP voice and tone specs, the RFP-content-library pointer, the deal-desk-approved boilerplate-vs-customizable rules per section, the default validity and escalator language, the SME contact tree, the legal-review trigger (which kinds of buyer requirements trigger a legal pre-clear), and the win-probability-tier-driven effort budget
- Reference `knowledge-base/terminology/` for correct industry terminology (3PL / 4PL / managed transportation / dedicated contract carriage / capacity assurance / detention-and-demurrage / vendor-compliance / accessorial / SOC 2 / TAPA / C-TPAT / EDI 856 / EDI 944 / EDI 947 / EDI 940 / WMS / TMS / OMS / OTIF / CCR / IFR / DPMO / dim-weight / cube-utilization)
- Reference `knowledge-base/regulations/us-tariff-authorities-overview.md` if the RFP touches cross-border lanes or duty-bearing commodities (the buyer's landed-cost view should not be promised as the company's responsibility unless the contract explicitly assigns it)
- Reference `knowledge-base/regulations/usmca-rules-of-origin.md` if cross-border North-American volume is in scope and the buyer expects origin-determination support on the response
- Reference `knowledge-base/regulations/eu-trade-regulations-overview.md` if the RFP includes EU lanes, EU customer-data, or EU sustainability-disclosure expectations
- Reference `knowledge-base/best-practices/logistics-platform-security-controls.md` for the security-section block on the operator-IT-control story (FIDO2 / phishing-resistant MFA, DMARC enforcement, mailbox-monitoring, IR-runbook, vendor-side security controls)
- Reference `knowledge-base/best-practices/claims-fraud-indicators.md` for the cargo-loss-prevention / claims-handling section block
- If a previous RFP response to this same buyer lives in `outputs/`, load it as the starting point — most renewal RFPs reuse 60–80% of the prior cycle's content, and the buyer's questions across cycles trend toward consistency

**Process:**

1. **Decompose the RFP** — Read through the package and produce the full **Section Map**: each section, its required format (narrative / table / yes-no / checkbox / file-attachment), any character or page limits, the buyer's evaluation weight if disclosed, the prerequisite SMEs, and the controlling deadline if it differs from the master submission deadline (some buyers require interim certifications or pre-bid Q&A submissions before the main response). Flag any section the team has not encountered before — those go to the *new-content* pile rather than the *library-pull* pile
2. **Pull the library blocks** — For every section the library covers, pull the most recent block, verify its last-reviewed date is within the company's freshness window (default 6 months for technology-stack and security blocks, 12 months for operational-capability blocks, 24 months for company-history blocks), and surface any block marked do-not-reuse or needs-SME-refresh. For blocks beyond the freshness window, mark the section as *library-pull-needs-SME-refresh* rather than treating it as ready content
3. **Customize each block for the shipper** — Substitute the buyer's specific commodity, volume, lane, and SLA into the block's parameterized text. Replace generic capability statements with statements tied to the buyer's actual ask ("We operate 12 cross-dock facilities" → "Our cross-dock network in the buyer's named lanes — Memphis, Dallas, Stockton — runs 24/7 with same-day inbound-to-outbound for volume up to the buyer's stated peak"). Remove any block-internal language that the buyer's RFP explicitly contradicts (e.g., do not include the standard 30-day-payment-terms boilerplate if the RFP names 60-day terms as a minimum requirement). Note customizations that materially change the company's commitment — these need an SME pass even if the underlying block was library-clean
4. **Draft net-new content** — For sections the library does not cover, draft net-new narrative in the company's RFP voice. Each net-new section should be flagged at the top as *new content — needs SME pass before submission* so the SME knows to read with care rather than to skim. After SME approval, the deal desk decides whether the new content goes back into the library as a future-reusable block (with a last-reviewed date set)
5. **Build the pricing section** — Per the buyer's row format, populate the pricing rows from the cost-basis input. Each row should show the all-in rate the company is committing to plus the cost components in a transparency layer (line-haul + fuel + accessorial schedule with per-event prices and trigger conditions for transportation; warehouse-storage-rate + handling-rate + value-add-services menu for warehousing; fulfillment-per-order + pick-pack + packaging + return-handling for fulfillment). Attach the validity window, the fuel-surcharge mechanism, the GRI / escalator language, and the price-protection clause. For any rate below the configured floor, surface the named approver and route the row through the approval matrix. For multi-lane responses, route the rate strategy to `skills/sales/spot-vs-contract-rate-negotiation-brief.md` and bring the result back into the pricing rows here
6. **Assemble the references and case studies** — Pull two or three named-reference-with-permission entries that match the buyer on commodity, volume, geography, and service mix. Where named references are unavailable, use anonymized case studies that meet the same match criteria. Each entry should be a tight paragraph: the customer's situation in their own words (paraphrased), the company's solution, the measured outcome (KPIs in numbers, not adjectives), and the duration of the relationship. Avoid the temptation to over-pad — three strong entries beat seven generic ones
7. **Run the compliance-and-disclosure pass** — Populate the compliance and disclosure section against the buyer's specific requirements. Insurance limits and the COI templates the buyer expects; SOC 2 / ISO / TAPA / C-TPAT / FSMA / hazmat / DOT-broker-authority status with the certifying-body and date; sustainability disclosure aligned to the 2026 Omnibus-narrowed CSRD scope (most U.S. mid-market 3PLs are out of scope; the response should state the company's voluntary reporting framework rather than imply CSRD obligation); DEI / DBE certification status; financial-statements posture with the NDA-required flag if applicable. Where the buyer's requirement exceeds the company's current posture, route to legal and the deal desk for a decision before claiming compliance
8. **Run the legal-and-contract-terms pass** — Identify any buyer-supplied contract language, flow-down requirement, indemnity clause, limitation-of-liability change, data-processing addendum, AI-use-disclosure clause, or termination-for-convenience term that diverges from the company's standard. Surface every divergence in a single legal-review block with the company's standard, the buyer's ask, and the deal-desk-recommended position (accept / counter / decline). The legal-review block is the gate; the response does not commit to non-standard terms until the gate clears
9. **Build the workflow tracker** — Produce the per-section workflow status: section, owner, library-pull status, customization status, SME-pass status, legal-review status if applicable, final-compile status. The tracker is the artifact the deal-desk owner uses to run the SME passes against the deadline. For larger RFPs (more than 12 sections), the tracker should also call out the critical path so the sequencing of SME passes does not block on a single bottleneck
10. **Compose the submission package** — In the buyer's required submission format (PDF, Word, Excel pricing tab, in-portal answer fields, or a multi-file ZIP), assemble the response with the section ordering and the section labels the buyer specified. Apply the buyer's required fonts, page limits, character limits, and formatting requirements (some buyers reject responses that exceed character limits in portal fields or that diverge from a required template). Include a one-page **executive summary** at the front that names the company, the deal owner, the named procurement contact, the response's distinctive value points (three at most), and the submission's table of contents
11. **Run the submission-ready trust check** — Before declaring the response submission-ready, scan for: (a) any library block whose customization is incomplete, (b) any net-new content that has not had an SME pass, (c) any pricing row below floor without a named approver, (d) any compliance claim the company cannot document, (e) any legal-term commitment outside the company's standard without a legal-review clear, (f) any reference or case study used without the customer's documented permission, (g) any character or page limit the response violates, (h) any submission-channel requirement (file format, file naming, portal field structure) the package does not meet, (i) any post-submission obligation (Q&A windows, oral presentations, BAFO rounds) the deal desk has not yet scheduled. Flag rather than silently fix — a response that submits with a hidden gap is a response that loses the deal in the SME-defense round

**Output requirements:**

- A **one-page executive summary** at the top of the response: company entity, deal owner, named procurement contact, the three distinctive value points tied to the buyer's specific ask, and the submission's table of contents
- A **section-by-section response** in the buyer's required ordering and labeling, with each section's content tied to its source (library block ID and last-reviewed date for library-pull, *new content* tag for net-new content, customization notes for blocks that were materially adapted)
- A **pricing section** with the row format the buyer requires, transparency layers behind each rate, validity-and-fuel-mechanism language, and the approval-matrix audit trail for any below-floor row
- A **references and case studies** section with two or three matched entries, each with permission status documented
- A **compliance and disclosure** section with the certifications, insurance, sustainability framework, and DEI/DBE entries the buyer asked for; gaps relative to the buyer's requirement explicitly flagged with the route the deal desk took
- A **legal-review block** (internal-only by default; sometimes also part of the submission cover) listing every divergence from the company's standard contract language with the recommended position
- A **workflow tracker** (internal-only): section / owner / status across library-pull, customization, SME pass, legal review, final compile
- An **Assumption Register** (internal-only): Field | Assumed value | Source (config / library / buyer-RFP-inference) | Confidence | Confirm-by gate — every field filled from a default rather than supplied input, so the deal desk confirms in one pass instead of being asked serially
- A **consolidated clarification block** (internal-only): the small set of genuine blockers that cannot be safely assumed — approved cost basis for the pricing rows, reference-permission status, non-standard legal-term positions — posed once, not as serial questions
- An **internal deal-desk note** capturing assumptions (cost basis, library version, references-permission status, legal-clears-pending), the win-probability tier, the named approver chain, and the submission-time gate
- The **submission package** in the buyer's required format, with the buyer's required fonts, page-limit, character-limit, and file-naming compliance verified
- Saved to `outputs/<rfp-id>/` if the user confirms

## Example Output

Reference output (illustrative — mid-market shipper RFP, 8-section TL + LTL managed-transportation scope, challenger position, target win-probability tier).

**Executive summary (page 1 of submission)**

| Field | Value |
|---|---|
| Responding company | Meridian Freight Partners LLC |
| Deal owner | K. Adeyemi, VP Sales |
| Procurement contact | T. Nguyen, Supply Chain Director, Hartfield Consumer Goods |
| Submission deadline | 2026-05-23 17:00 ET |
| Submission channel | Hartfield procurement portal (RFP-2026-HCG-04) |
| Win-probability tier | Target |

**Three distinctive value points (tied to Hartfield's stated requirements):**
1. Single-system visibility across TL and LTL via Project44 integration covering 99% of Hartfield's named carrier roster — matching the buyer's "carrier-agnostic real-time tracking" requirement.
2. Dedicated lane-management team for Hartfield's Memphis–Atlanta corridor (46% of bid volume) — addressing the buyer's stated dissatisfaction with incumbent's reactive-only model.
3. Proven 99.1% OTIF on comparable CPG volume (see reference: Springdale Household Products, Section 7) — directly responsive to the buyer's 98.5% OTIF SLA floor.

**Table of contents:** Section 1: Company Overview | Section 2: Operational Capability | Section 3: Technology & Integration | Section 4: Security & Compliance | Section 5: Sustainability | Section 6: Pricing | Section 7: References | Section 8: Terms & Conditions Response

---

**Section map (internal workflow view)**

| Sec | Title | Format | Char / pg limit | Buyer weight | Source | SME | Status |
|---|---|---|---|---|---|---|---|
| 1 | Company Overview | Narrative | 2 pp | 5% | Library block LIB-CO-003 (last reviewed 2026-02-14, within 365-day window) | — | ✅ library-pull |
| 2 | Operational Capability | Narrative + table | 4 pp | 25% | Library block LIB-OC-011 (last reviewed 2025-11-30, within 365-day window) + buyer-specific lane customization | Ops (R. Patel) | 🔶 needs SME pass |
| 3 | Technology & Integration | Narrative + EDI checklist | 3 pp | 20% | Library block LIB-TECH-007 (last reviewed 2026-01-18, 180-day window ✅) | IT (M. Chen) | 🔶 needs SME pass |
| 4 | Security & Compliance | Narrative + cert table | 2 pp | 10% | Library block LIB-SEC-002 (last reviewed 2026-03-05, 180-day window ✅) | IT / Compliance | ✅ library-pull |
| 5 | Sustainability | Narrative | 1 pp | 5% | Net-new (no library block covers Hartfield's Scope 3 carrier-emissions-data ask) | Sustainability (D. Osei) | 🔴 new content — needs SME pass |
| 6 | Pricing | Excel tab (provided template) | — | 25% | Deal desk + rate input from `freight-quote-response-drafter.md` runs for 12 named lanes | Finance / Deal desk | 🔶 pricing approval pending |
| 7 | References | 3 named entries | 1 entry per page | 5% | LIB-REF-Springdale (permission confirmed 2026-04-10) + LIB-REF-Lakewood (permission confirmed 2026-03-22) + LIB-REF-Centara (permission pending — do not include until confirmed) | Acct team | 🔴 Centara permission outstanding |
| 8 | T&C response | Buyer-supplied redline | — | 5% | Legal review required (buyer's limitation-of-liability clause diverges from standard) | Legal (J. Park) | 🔴 legal gate open |

---

**Section 2 — Operational Capability (library-pulled + customized, excerpt)**

*[Library block LIB-OC-011 baseline text — buyer-specific customizations shown in brackets]*

Meridian Freight Partners operates a managed-transportation model for [shippers with 200–2,000 annual truckload equivalents in the CPG and general-merchandise sector], coordinating a vetted carrier roster of [420 active carriers] across [28 states]. Our model centers on a dedicated account team — not a shared-services pool — so the [Hartfield] account has named personnel who know the [Memphis–Atlanta] corridor's capacity dynamics, the [Walmart Neighborhood Market DC] appointment windows, and the [seasonal peak in Q4 associated with Hartfield's promotional calendar].

For [Hartfield's bid volume of approximately 1,800 TL and 3,200 LTL shipments annually], Meridian would assign a [2-person dedicated lane team] responsible for [daily tender management, carrier performance scoring, and a weekly 30-minute operational review with Hartfield's supply chain team].

*[Customization notes: "CPG and general-merchandise sector" added for Hartfield; "420 active carriers" and "28 states" from current carrier master (ops SME to confirm current count before submission); "Memphis–Atlanta corridor" and "Walmart Neighborhood Market DC" specific to Hartfield's RFP Section 2.3 requirement. Ops SME (R. Patel) must confirm carrier count and dedicated-team staffing model before this section is released.]*

**Section 2 — SME-pass flag:** *NEW CONTENT — needs SME pass before submission.* Customized lane-team staffing model (2 FTEs dedicated to Hartfield) requires R. Patel sign-off on capacity to commit.

---

**Section 6 — Pricing (representative rows, Excel tab format)**

| Lane | Origin | Destination | Equipment | Rate basis | All-in rate | FSC mechanism | Validity | Margin vs. floor |
|---|---|---|---|---|---|---|---|---|
| MEM–ATL-01 | Memphis, TN (38118) | Atlanta, GA (30336) | 53' dry van | Per mile | $2.41/mi | DAT index (weekly avg) × 18.5% of linehaul | Valid thru 2026-08-31 | Above floor ✅ |
| MEM–ATL-02 LTL | Memphis, TN (38118) | Atlanta, GA (30336) | LTL, class 85 | Per cwt | $28.60/cwt | Estes FSC schedule applied at tender | Valid thru 2026-08-31 | Above floor ✅ |
| CHI–MEM-01 | Chicago, IL (60609) | Memphis, TN (38118) | 53' dry van | Per mile | $2.28/mi | DAT index (weekly avg) × 18.5% of linehaul | Valid thru 2026-08-31 | Above floor ✅ |

*Pricing approval required from Finance (J. Tran) before submission. Margin detail on file in deal-desk internal notes — not included in buyer submission.*

*Rate strategy for multi-lane negotiation: routed through `spot-vs-contract-rate-negotiation-brief.md` — Hartfield's stated preference for contract rates on the MEM–ATL corridor means spot-market exposure acknowledged but pricing anchored to 12-month contract with quarterly GRI review.*

---

**Workflow tracker**

| Step | Owner | Due | Status |
|---|---|---|---|
| RFP receipt + kickoff | K. Adeyemi | 2026-05-12 | ✅ |
| Section map + library-pull | Deal desk | 2026-05-13 | ✅ |
| Ops SME pass (Sections 2) | R. Patel | 2026-05-16 | 🔶 in progress |
| IT SME pass (Section 3) | M. Chen | 2026-05-16 | 🔶 in progress |
| Sustainability net-new draft (Section 5) | D. Osei | 2026-05-17 | 🔴 not started |
| Centara reference permission | Acct team | 2026-05-17 | 🔴 outstanding |
| Pricing approval | J. Tran | 2026-05-19 | 🔶 in progress |
| Legal T&C redline (Section 8) | J. Park | 2026-05-20 | 🔴 not started |
| Final compile + QA | Deal desk | 2026-05-22 | — |
| Submission (24-hr buffer) | K. Adeyemi | 2026-05-23 by 17:00 ET | — |

**Critical path:** Legal review (J. Park) and sustainability net-new (D. Osei) are both gating; both must clear by 2026-05-20 to preserve the compile window. Centara reference is non-blocking if the other two references are confirmed.

---

**Assumption Register (internal-only — drafted from defaults, confirm during SME pass)**

| Field | Assumed value | Source | Confidence | Confirm-by gate |
|---|---|---|---|---|
| Company entity + RFP voice | Meridian Freight Partners LLC; deal-desk operational voice | config (`rfp.voice`) | High | Compile QA |
| Active carrier count / states | 420 carriers / 28 states | library block LIB-OC-011 | Med — count may be stale | Ops SME pass (R. Patel) |
| Block freshness windows | 180 d tech/security, 365 d operational | config (`rfp.block_freshness_days`) | High | — |
| Win-probability effort budget | Target tier | config (`rfp.win_tier_effort_hours`) | High | Deal owner confirm |
| CSRD scope | Out of scope (US mid-market, Omnibus-narrowed) | config (`rfp.compliance.csrd_scope_eligible: false`) | High | Sustainability SME (D. Osei) |
| Dedicated lane-team staffing | 2 FTE for Hartfield | buyer-RFP-inference (volume ÷ standard span) | Low — commitment-bearing | Ops SME pass (R. Patel) |

*Six fields were filled from defaults rather than asked; only the three true blockers (cost basis, Centara reference permission, Section 8 legal position) were escalated as questions — see the consolidated clarification block, not a serial Q&A.*

**Submission-ready trust check**

| # | Check | Status |
|---|---|---|
| a | All library blocks within freshness window | ✅ All blocks ≤ 365 days (tech/security ≤ 180 days) |
| b | All net-new content has had SME pass | 🔴 Section 5 (sustainability) not yet SME-reviewed |
| c | All pricing rows above configured floor | ✅ (three rows shown; full tab requires Finance approval) |
| d | All compliance claims documented | ✅ SOC 2 Type II (2026-02-28), COI on file, no CSRD obligation (US mid-market, Omnibus-scoped out) |
| e | No non-standard legal terms committed without legal-review clear | 🔴 Section 8 limitation-of-liability divergence — legal gate open |
| f | All references have documented permission | 🔴 Centara permission outstanding — do not include until confirmed |
| g | No character / page limits violated | ✅ (narrative sections checked; pricing tab format matches buyer template) |
| h | Submission-channel requirements met | ✅ Hartfield portal accepts PDF + Excel tab as separate uploads |
| i | Post-submission obligations scheduled | ✅ Oral presentation scheduled 2026-06-03; BAFO round expected 2026-06-10 |

**Response not submission-ready.** Three open gates: Section 5 SME pass (D. Osei), legal T&C clear (J. Park), Centara reference permission (acct team). All must close before the 2026-05-22 compile step.

## Configuration Reference

- `config.yml` — `rfp.voice` (deal-desk-edited operational voice), `rfp.library_path`, `rfp.block_freshness_days` (default 180 / 365 / 730 by block type), `rfp.deadline_buffer_hours` (default 24), `rfp.win_tier_effort_hours` (commit / target / stretch), `rfp.legal_review_trigger_terms` (list), `rfp.compliance.insurance_min`, `rfp.compliance.soc2_status`, `rfp.compliance.csrd_scope_eligible` (default false for most US mid-market 3PLs per 2026 Omnibus narrowing), `rfp.references.permission_required` (true)
- Knowledge base — `knowledge-base/terminology/` (3PL / 4PL / WMS / TMS / EDI / OTIF / CCR / DPMO definitions), `knowledge-base/best-practices/logistics-platform-security-controls.md` (security-section content basis), `knowledge-base/best-practices/claims-fraud-indicators.md` (claims-and-loss-prevention content basis), `knowledge-base/regulations/` (cross-border, USMCA, EU posture)
- Sibling skills — `skills/sales/freight-quote-response-drafter.md` (per-quote pricing rows the company plugs into the RFP pricing section), `skills/sales/load-tender-response-drafter.md` (tendered-load post-award), `skills/sales/spot-vs-contract-rate-negotiation-brief.md` (rate strategy on multi-lane pricing rows)
