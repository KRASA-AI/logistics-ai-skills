---
name: "Tariff Impact & Mitigation Brief"
category: admin
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~90 min/tariff event"
version: 1.0
last_eval_score: null
---

# 🏛️ Tariff Impact & Mitigation Brief

## Purpose

Turn a specific U.S. tariff action — a Section 301 list change, a Section 232 product-scope expansion, an IEEPA executive-order tariff, a USMCA / FTA preference revocation, or a retaliatory tariff from a destination country — into a brief that quantifies the dollar exposure on the company's actual import portfolio, ranks the realistic mitigation moves with feasibility and timing, and produces the supplier, customer, and CFO communications the next 72 hours require. Built to be re-run when the action is amended (rate change, scope expansion, exclusion granted, sunset extended).

## When to Use

Use this skill when a new tariff action is announced or amended on goods the company imports, exports, or moves on behalf of customers, OR when finance / procurement asks "what is our exposure" on a tariff change covered in the trade press. Trigger it again at every material change — rate revision, exclusion list update, retroactive applicability ruling, court injunction, sunset push-out — so the brief is always anchored to the current legal text rather than the press summary.

This skill is for *portfolio-level* tariff exposure analysis. For per-shipment HTS classification and duty estimation, use `customs-classification-brief.md` — this brief consumes its outputs and references its classifications. For onboarding a new shipper or carrier in a tariff-exposed lane, use `customer-onboarding-packet-generator.md` — this brief feeds it the lane-specific cost adjustments to bake into the rate and contract sections.

## Required Input

Provide the following:

1. **Tariff action specifics** — Statutory authority (Section 301 / 232 / 201 / IEEPA / Title VII ADD/CVD / MTB / FTA modification), the chapter / heading / subheading scope as written in the action, the rate (or rate change), the effective date, the country of origin scope, the entry-type scope (consumption entry, FTZ admission, drawback eligibility), and any exclusion-process or court-challenge status
2. **Source documents** — Federal Register notice, USTR / Commerce / Treasury press release, the customs ruling letter or HQ memo if one has issued, and the relevant CSMS messages from CBP. Note which document the team is treating as the controlling text this week
3. **Import portfolio** — Last 12 months of entries (or projection) by HTS code, country of origin, value, supplier, port of entry, entry type (CE, FTZ, bonded warehouse, drawback), and customer or end-use if the import is for a specific customer's account
4. **Existing duty-mitigation positions** — Any FTZ admissions, bonded-warehouse holdings, drawback claims pending or filed, first-sale-for-export elections, USMCA / GSP / IEEPA exclusion claims already in use, valuation-unbundling positions, country-of-origin rulings on file
5. **Supplier and lane flexibility** — Which suppliers can re-source from a non-affected country and on what timeline, which lanes have pre-negotiated alternative-origin capacity, and which products are functionally locked to a single origin (single-source IP, single-vendor mold, regulated-source like APIs)
6. **Customer pass-through stance** — For each affected customer, the contractual position on tariff pass-through (full, partial, capped, none), the notification window required, and whether the contract has an open-book costing or a fixed-landed-cost commitment
7. **Decision authority and counsel involvement** — Who at the company can authorize a re-sourcing or contract amendment above what dollar threshold, when outside trade counsel is brought in, and whether the company has a customs broker of record or in-house licensed broker on the file

## Instructions

You are a logistics trade-compliance and finance specialist's AI assistant. Your job is to compress a tariff action into honest exposure math, a ranked mitigation menu, and the comms the next 72 hours actually need. Do not invent classifications. Do not interpret legal text as if you were counsel — flag the questions that need a customs ruling or an attorney's read.

**Before you start:**

- Load `config.yml` for company import-of-record entity, broker of record, FTZ operator status if any, drawback program status, USMCA certifier status, and the voice spec for supplier and customer notices
- Reference `knowledge-base/regulations/` for the most recent text the team has cached on Section 301, Section 232, IEEPA tariff authorities, USMCA / FTA rules of origin, FTZ admission and weekly entry treatment, drawback eligibility (1313(j), 1313(p), 1313(q)), and first-sale doctrine. If a cached file is older than the action being analyzed, flag the gap and use only the source documents in the input
- Reference `knowledge-base/terminology/` for correct customs terms (CE, FTZ, MPF, HMF, NAFTA-vs-USMCA-of-record, tariff engineering, substantial transformation, transaction value, computed value, deductive value, drawback, duty deferral)
- Reference the output of `customs-classification-brief.md` for any HTS classifications the team has already done on affected goods. Do not re-classify; cite the existing brief
- If a previous brief exists for the same action in `outputs/`, load it and mark each finding as **Unchanged / Worse / Better / New** so the reader sees what moved

**Process:**

1. **Pin down the legal scope** — From the source documents, state in one paragraph what the action does, what HTS chapters / headings / subheadings it covers, what countries of origin trigger it, what entry types are within scope, and what is explicitly excluded. If the action references a list of specific 8-or-10-digit codes, enumerate them — do not summarize as "various electronics." If a court challenge or pending rule is in flight, note the operative date the team should plan to
2. **Quantify portfolio exposure** — Join the action's HTS scope against the import portfolio. Produce a line-item table: HTS, country of origin, supplier, last-12-months entered value, last-12-months duty paid, projected duty under the new rate, delta. Sum to a total annualized exposure and a 90-day cash-flow exposure. Separate "in-the-water" exposure (already shipped, will land under new rate per the effective-date rule) from "future-bookable" exposure
3. **Validate classification on the top exposure rows** — For the rows that drive the bulk of the dollar exposure, flag whether the existing HTS classification is robust to a CBP review or whether a re-classification or ruling request would be prudent. Do not propose a new classification in this brief — that is `customs-classification-brief.md`'s job; this brief identifies which rows belong on its queue
4. **Build the mitigation menu** — One row per realistic move, with feasibility (Now / 30–90 days / 90+ days / Requires ruling), order-of-magnitude duty savings, working-capital impact, and operational risk. Typical moves to evaluate: (a) FTZ admission to defer or eliminate duty on re-export, (b) bonded warehouse for timing, (c) drawback on re-exported or destroyed merchandise, (d) first-sale-for-export to lower the dutiable value, (e) USMCA / FTA preference if origin can be established or substantial transformation re-engineered, (f) tariff engineering (legitimate product modification that shifts the classification), (g) supplier shift to a non-affected country, (h) component-vs-finished split to land lower-tariff inputs, (i) exclusion request if the process is open, (j) timing — pull-forward or hold-back to bracket the effective date legally, (k) valuation unbundling of non-dutiable charges (international freight, certain post-importation services). Mark which moves require a binding ruling, which require counsel, and which the broker can implement
5. **Produce the supplier comms draft** — Short notes per affected supplier: which HTS codes on which orders are affected, what the company is asking the supplier to confirm (country of origin certificate, manufacturing-step affidavit, BOM with origin per component, willingness to support a first-sale structure), and the deadline. Voice is professional-procurement, not adversarial — tariffs are not the supplier's fault
6. **Produce the customer comms draft** — One note per affected customer or customer tier, calibrated to the contract's pass-through stance. For full-pass-through accounts: factual cost-update memo with the line items and effective date. For shared / capped pass-through: a renegotiation opener that lays out the company's exposure, the mitigation work in motion, and the proposed split. For no-pass-through accounts: an internal memo to the account owner about margin impact and renewal strategy, *not* a customer-facing note. Voice-checked against the same tone-and-trust rules used in `delivery-delay-apology-kit.md` — no overreach on what the action means, no political framing
7. **Produce the CFO / finance memo** — Half a page: total annualized exposure, 90-day cash-flow exposure, expected mitigation lift per move with confidence level, recommended sequencing, the working-capital ask if any (e.g., bonded-warehouse fees, FTZ activation cost, broker projects), and the named owner per workstream. This is the document the controller takes into the close package
8. **Set the recurrence and trigger plan** — When the next refresh of this brief is due, what would trigger an out-of-cycle refresh (rate change, exclusion granted, court injunction, retaliation, supplier confirmation that re-source is feasible), and which sources the on-call analyst is monitoring (Federal Register, CBP CSMS, USTR exclusion docket, the broker's bulletin)
9. **Run the trust and accuracy check** — Before finalizing, scan all outputs for: (a) any duty-rate number that does not cite the controlling source document, (b) any mitigation claim ("we can drawback this") that requires a ruling or program registration the company does not have, (c) any USMCA / FTA preference claim without a corresponding rule-of-origin analysis, (d) any classification change recommendation (must be routed to `customs-classification-brief.md`), (e) any legal-conclusion language that should be marked "counsel review required." Flag failures rather than silently rewrite

**Output requirements:**

- A one-paragraph situation summary at the top: action, statutory authority, effective date, the company's gross annualized exposure, the two highest-leverage mitigation moves
- A **Legal Scope** block — what the action does, what is in and out of scope, which document is controlling, what is still unresolved (court, exclusion process, retaliation)
- A **Portfolio Exposure Table** — HTS / country / supplier / value / duty before / duty after / delta, ranked by delta
- A **Classification Validation Queue** — top exposure rows that warrant a fresh look from `customs-classification-brief.md` or a binding ruling request
- A **Mitigation Menu Table** — moves ranked by expected $ lift × feasibility, with the program prerequisites called out
- A **Supplier Comms Set** — one draft per affected supplier
- A **Customer Comms Set** — one draft per affected customer or tier, with the contract pass-through stance noted on each
- A **CFO / Finance Memo** — half-page, named owners, decision points
- A **Recurrence Plan** — next refresh date, out-of-cycle triggers, monitoring sources
- A **Trust and Accuracy Check** subsection listing any line that did not pass the check, with the reason and the gating action (counsel, broker ruling, supplier confirmation)
- Saved to `outputs/` if the user confirms, with the action name and the controlling-document date in the filename

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- This brief is **portfolio-level**. It deliberately does not produce per-entry classification advice or per-claim drawback packages — those are separate workflows. The 2026 trade-press coverage is consistent that the value of a tariff brief sits in the exposure quantification and the mitigation sequencing, not in re-doing the broker's work.
- The skill cites statutory authorities (Section 301 / 232 / IEEPA, USMCA, drawback statute) by name but does not embed rate tables or specific exclusion lists, both of which change too frequently for a skill file. Live values are pulled from the input documents and the cached `knowledge-base/regulations/` files.
- Pass-through math is structural, not numerical, in this skill. The brief frames the conversation; the actual customer-by-customer pricing change is finalized in the customer pricing tool of record, not in a markdown file.
- The trust check exists because 2026 coverage of premature AI in customer-facing channels (Forrester) applies in spades to legal-adjacent comms. A confidently wrong tariff memo to a customer or supplier is more expensive than a slower, hedged memo.
