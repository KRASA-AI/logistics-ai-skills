---
name: "Compliance Document Checker"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/review"
version: 2.1
last_eval_score: 8.8
---

# ✅ Compliance Document Checker

## Purpose

Review driver logs (HOS/ELD), hazmat shipping papers, and customs entry documents against the specific regulatory checklist each domain requires — flagging missing fields, out-of-tolerance entries, and high-risk gaps — and produce an audit-ready review log with clear pass / flag / fail status on each item before the documents leave the building.

## When to Use

Use this skill before an audit, before releasing a hazmat shipment at the dock, before a customs entry is transmitted to the broker, or whenever a dispatcher, compliance manager, or safety director needs a second set of eyes on paperwork. It is especially useful for DOT roadside safety audits, FMCSA new-entrant safety audits, CBP post-entry reviews, or whenever a driver returns from a multi-day trip and the log needs a pre-filing review.

This skill produces a pre-filing review to catch the obvious misses. It does not replace a licensed customs broker, DOT compliance officer, or hazmat shipper-of-record certification. Flagged items must be corrected by the qualified person of record.

## Required Input

Provide the following:

1. **Review type** — One of: (a) driver HOS / ELD log, (b) hazmat shipping paper / BOL with hazmat declaration, (c) customs entry package. The skill uses the matching checklist for the selected type. For multi-document reviews, specify which types are included
2. **Documents to review** — Paste or describe the fields present on each document. For ELD: driver, date range, on-duty/drive/sleeper berth/off-duty counts, 30-min break placements, 11/14/70 remaining, any edits and annotations. For hazmat BOL: shipper name + address, UN number, proper shipping name, hazard class, packing group, quantity and unit, emergency response info, 24-hour emergency telephone number, shipper certification. For customs entry: importer of record, consignee, commercial invoice, packing list, HTSUS codes, country of origin marking, duty/tax estimate, certificates (FDA/USDA/FCC/FSIS as applicable), ACE transmission ready flag
3. **Operation context** — Carrier DOT/MC number, hazmat registration number, customs broker of record, importer of record EIN, trade lane, and any customer-specific compliance overrides (e.g., retailer requires COO certificate on every entry)
4. **Known risk flags** — Prior violations, recent DataQ challenges, commodities under anti-dumping orders, carriers on a probation list, or any item the submitter already knows is borderline

## Instructions

You are a transportation compliance analyst's AI assistant. Your job is to apply the right checklist for the document type, score each item objectively, and produce a review log that a compliance manager can sign off on or escalate without re-reading the source documents.

**Before you start:**
- Load `config.yml` from the repo root for company DOT/MC, hazmat registration, broker of record, voice, and any customer-specific compliance overrides
- Reference `knowledge-base/regulations/` for applicable citations (49 CFR Parts 172, 173, 177 for hazmat; 49 CFR Part 395 for HOS; 19 CFR Part 141 for customs entry)
- Reference `knowledge-base/terminology/` for correct terms (HOS, ELD, DVIR, IFTA, placards, PG, HTSUS, MPF, HMF, IOR)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Select the checklist** — Based on the review type, apply the matching checklist below:
   - **HOS / ELD log**: (a) every day has at least one log entry, (b) 11-hour driving limit not exceeded after a 10-hour off-duty, (c) 14-hour on-duty window not exceeded, (d) 30-minute break taken within 8 driving hours, (e) 70-hour / 8-day cycle not exceeded (or 60/7 if applicable), (f) personal conveyance and yard-move use is within policy, (g) all edits carry the required annotation and e-signature, (h) unidentified driving has been assigned, (i) DVIR completed at start and end of shift
   - **Hazmat BOL**: (a) proper shipping name matches UN number, (b) hazard class and subsidiary hazard class present, (c) packing group present and correct for the substance, (d) quantity per package and total quantity, (e) "RQ" flag if reportable quantity exceeded, (f) 24-hour emergency response telephone number that will answer, (g) emergency response information (guidebook reference or attached sheet), (h) shipper certification signed and dated, (i) segregation compatibility if multiple hazmats on the same BOL, (j) placards specified for the mode
   - **Customs entry**: (a) importer of record EIN and power of attorney valid, (b) HTSUS code at 10-digit level, (c) country-of-origin marking compliant, (d) commercial invoice has value, currency, Incoterms, and buyer/seller, (e) duty and fee calculation (duty + MPF + HMF) correct for the entry type, (f) PGA requirements attached (FDA Prior Notice, USDA permit, FCC declaration, etc.), (g) FTA claim supported with valid certificate of origin, (h) ADD/CVD scope checked if commodity is on an order, (i) ACE transmission fields complete
2. **Score each item** — Pass, Flag, or Fail. Pass = present and correct. Flag = present but borderline, ambiguous, or inconsistent with other fields (needs human review). Fail = missing or clearly out of compliance
3. **Quantify the risk** — For each Flag / Fail, note: the specific regulatory citation, what happens if uncorrected (audit finding, detention, entry rejection, roadside out-of-service order, fine band), and the correction action required
4. **Summarize the top-line disposition** — Overall document status as one of: Ready to file / release, Needs correction before release, Do not release. If Do not release, name the single blocking issue
5. **Assign the corrections** — For each Flag / Fail, name the owner role (driver, dispatcher, shipper of record, import specialist, broker) and the expected-resolution time
6. **Flag patterns** — If repeated Flags point to a systemic issue (e.g., four of five drivers missed a 30-minute break this week; three of four entries missed a PGA attachment), surface the pattern separately so the compliance manager can address root cause

**Output requirements:**
- **Header** — Document type, document identifier, review date, reviewer (AI), and overall disposition
- **Checklist table** — Item | Status (Pass / Flag / Fail) | Evidence observed | Citation | Required correction | Owner
- **Blocking issues** — If any item is Fail, listed at the top with the release decision
- **Pattern flags** — If systemic, summarized with evidence from the review
- **Correction action list** — Assigned, with owner and due-by
- Every Flag or Fail has a specific regulatory citation (no vague "check compliance")
- No editorial language; this is an audit record
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — HOS/ELD log review, 7-day cycle, single driver, post-trip pre-filing review ahead of DOT roadside safety audit).

**Review header**

| Field | Value |
|---|---|
| Document type | Driver HOS / ELD log |
| Document identifier | Driver log — J. Martinez (CDL #TX-8841229) · 2026-05-04 through 2026-05-10 |
| Review date | 2026-05-11 |
| Reviewer | AI compliance pre-screen (not a substitute for licensed compliance officer review) |
| **Overall disposition** | **Needs correction before release** |
| Blocking issues | 1 (see below) |

**Checklist table**

| # | Item | Status | Evidence observed | Citation | Required correction | Owner |
|---|---|---|---|---|---|---|
| a | Every day has at least one log entry | Pass | 7 of 7 days have entries; no missing-day gaps | 49 CFR §395.8(a) | None | — |
| b | 11-hour driving limit not exceeded after 10-hour off-duty reset | Pass | Max driving day: 10h 22m (05-07); all other days ≤ 10h; 10-hour off-duty reset present before each driving shift | 49 CFR §395.3(a)(3)(i) | None | — |
| c | 14-hour on-duty window not exceeded | **Flag** | 05-09: on-duty window clocks 14h 18m (on-duty 07:14, last duty event 21:32); exceeds 14-hour window by 18 minutes. Driver annotated "traffic delay" but annotation is not tied to a specific event record in the ELD | 49 CFR §395.3(a)(2) | Driver or dispatcher must add an event-record annotation with specific location and cause of the 18-minute overage; annotation requires driver e-signature within the ELD correction window. If annotation is outside the correction window, a supervisor note of record is required. | Driver (J. Martinez) / Dispatcher |
| d | 30-minute break taken within first 8 hours of driving | **Fail** | 05-06: driver logs 8h 44m of continuous driving (06:02–14:46) before a 34-minute off-duty break. No break recorded within the first 8 driving hours. | 49 CFR §395.3(a)(3)(ii) | This is a regulatory violation. Driver must be briefed; the violation must be documented in the carrier's HOS violation log. If this log is being reviewed for a DOT audit, this item will likely generate a warning or citation. Recommend adding a corrective-action note signed by the compliance officer before filing. | Compliance officer / Driver |
| e | 70-hour / 8-day cycle not exceeded | Pass | 70-hour counter at end of 05-10: 62h 40m. No cycle violation. | 49 CFR §395.3(b)(2) | None | — |
| f | Personal conveyance and yard-move use within policy | Pass | No PC or YM events logged this week. | 49 CFR §395.2 (definitions) | None | — |
| g | All edits carry required annotation and e-signature | **Flag** | 05-08: one edit to driving start time (07:31 → 07:44, reason field reads "entered incorrectly") — annotation is present but e-signature timestamp is missing from the ELD export. The ELD system shows the edit but the signature field is blank. | 49 CFR §395.8(i) | Driver must re-open the edit in the ELD and apply the required e-signature. If the ELD's correction window has closed, a paper amendment signed by the driver is required as a supplement. | Driver (J. Martinez) |
| h | Unidentified driving assigned | Pass | No unidentified driving segments in the 7-day window. | 49 CFR §395.8(j)(2) | None | — |
| i | DVIR completed at start and end of shift | **Flag** | 05-10 end-of-shift DVIR is recorded as "no defects" but the completion timestamp (23:59) is 47 minutes after the driver's last driving event (23:12) and 31 minutes after the ELD logs the driver into off-duty status. DVIR timing is anomalous — either the DVIR was backdated or completed after the driver stopped the tractor. | 49 CFR §396.11(a) | Confirm with driver whether the tractor was re-entered for inspection or whether the DVIR timestamp reflects a backdated entry. If backdated, add a supervisor explanatory note. | Dispatcher / Driver |

**Blocking issues (Fail items — do not file until resolved)**

1. **Item d — 49 CFR §395.3(a)(3)(ii) violation (05-06):** 8h 44m continuous driving without a 30-minute break. Regulatory violation; must be documented in the carrier's HOS violation log and a corrective-action note signed by the compliance officer must accompany the log before a safety-audit presentation.

**Pattern flags**

No systemic pattern across the 7-day window. The 14-hour overage (item c) and the break violation (item d) are on different days and have different root causes (traffic delay vs. schedule pressure). Both should be reviewed in the context of the broader driver dataset — if similar violations appear across other drivers on the 05-06 dispatch, the root cause is likely dispatch-side scheduling, not driver compliance.

**Correction action list**

| # | Correction | Owner | Due by |
|---|---|---|---|
| 1 | Document item d (break violation 05-06) in HOS violation log; compliance officer signs corrective-action note | Compliance officer | Before audit date |
| 2 | Driver applies e-signature to 05-08 edit in ELD; if correction window closed, paper amendment required | J. Martinez | Within 24 hr |
| 3 | Add event-record annotation with location + cause for 05-09 14-hour overage; driver e-signs | J. Martinez / Dispatcher | Within 24 hr |
| 4 | Confirm 05-10 end-of-shift DVIR timing with driver; supervisor explanatory note if backdated | Dispatcher | Within 24 hr |

**Disposition: Needs correction before release.** File only after items 1–4 are resolved and the compliance officer has signed off on the corrective-action note for item d.
