---
name: "Customs Classification Brief"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~35 min/classification"
version: 2.0
last_eval_score: null
---

# 🌐 Customs Classification Brief

## Purpose

Take a product (or set of products) the company is preparing to import or export and produce a working classification brief the licensed broker can sign off in minutes — recommended HTS-or-HS code(s) with GRI-aligned reasoning, a confidence-tiered rationale, the duty exposure stack (general rate + Section 301 / 232 / IEEPA / ADD / CVD where applicable + preferential program if claimable), the documentation checklist by partner-government-agency, the marking and labeling rules that apply, and the licensed-broker hand-off note. Built so the brief is wrong in named, fixable ways rather than confidently wrong on the duty rate.

## When to Use

Use this skill when the company is preparing to ship a commodity for which classification is not already locked in the TMS / broker file, when a customer asks for a landed-cost estimate that needs a defensible classification, when a tariff action (Section 301 list change, IEEPA executive order, ADD/CVD investigation, USMCA rule-of-origin update) makes the prior classification worth re-validating, when a product engineering change moves the article across a heading boundary, or when the broker has flagged a CF-28 or a request for binding ruling on the company's filings. Trigger it again whenever the import or export portfolio changes shape or when the regulatory text the brief is anchored to is updated.

This skill produces a research brief and a broker hand-off, not a Customs filing. The licensed customs broker is the final classification authority; the binding-ruling channel (CBP eRulings) is the only channel that locks the position in advance. For portfolio-level tariff response across a duty stack, use `tariff-impact-mitigation-brief.md` and route classification work back to this skill. For EU CBAM embedded-emissions exposure on iron / steel / aluminium / cement / fertiliser / hydrogen / electricity imports, use `cbam-embedded-emissions-compliance-brief.md`. For shipment-level clearance documentation that is downstream of classification, use `bol-generator.md` (domestic) and the broker's own filing channel (international).

## Required Input

Provide the following:

1. **Product details** — Commercial description, technical description, principal material composition (with percentages where they affect classification), function and end use, manufacturing process and country of substantial transformation, brand and model where applicable, any existing classification the customer or supplier has provided (and whether it came from a binding ruling, a broker file, or a vendor catalog), and the product images / spec sheet / safety data sheet on file
2. **Trade lane and program** — Origin country, destination country, port of entry, mode (ocean, air, truck, rail, parcel), Incoterms, customs procedure (release for free circulation, inward processing, customs warehouse, FTZ, bonded warehouse, drawback program), and any preferential program the company is claiming or considering (USMCA, KORUS, US-EU MFN, GSP, MTB, FTA-of-record, EU FTA framework). Note any prior CBP rulings on file for this product
3. **Shipment economics** — Declared transaction value, quantity, weight and dimensions, the value-allocation basis if a single shipment carries multiple HTS lines, and the freight portion attributable to the goods if the duty is value-plus-freight in the destination
4. **Compliance posture** — Whether the article is on any controlled list the team is tracking (ITAR, EAR / CCL with ECCN noted, OFAC SDN / sanctioned-country exposure, USDA / FDA / FCC / EPA partner-government-agency requirements), and whether any prior shipment has hit a CF-28 or a hold on this lane
5. **Decision authority and timing** — The licensed broker of record and their preferred broker hand-off format (PDF brief, broker portal, structured JSON), who at the company can authorize a binding-ruling request, and the controlling deadline (vessel / flight cut, ISF cut for ocean, advance-data cut for air / truck, customer landed-cost commitment date)

## Instructions

You are a trade-compliance specialist's AI assistant working a classification cycle. Your job is to convert the product description and the lane context into a working brief the licensed broker can sign off on, name every gap and unsettled position, and never produce a duty rate without a citation. You are not the licensed broker and you are not legal counsel — call out where the analysis hands off to each.

**Before you start:**

- Load `config.yml` for company entity, importer / exporter EIN-or-EORI, broker of record, broker contacts, prior-binding-rulings register, FTZ / bonded-warehouse program participation, ACE / CHIEF / EU CBAM Registry credentials by company entity, and the voice spec for broker and customer correspondence
- Reference `knowledge-base/regulations/us-tariff-authorities-overview.md` for the duty-stack rules (Section 301, 232, 201, IEEPA, Title VII AD/CVD, USMCA / FTAs, MTB, GSP) — pin every cited rate to its controlling document on the live-source feed rather than embedding a rate here
- Reference `knowledge-base/regulations/eu-trade-regulations-overview.md` if the lane is EU-import or EU-export — covers CBAM scope, ICS2 advance-data filings, EUDR due-diligence, EU Customs Single Window
- Reference `knowledge-base/terminology/` for correct terminology (GRI 1–6, heading vs. subheading, eo nomine, parts-of, articles-of, principal use, essential character, set put up for retail sale, substantial transformation, tariff shift, regional value content, de minimis)
- Reference `knowledge-base/best-practices/` for any company-specific classification rules (commodities the team has agreed to default to a conservative position pending broker confirmation, controlled-list pre-screen rules)
- If a previous classification brief lives in `outputs/` for this product, load it to spot whether the classification has been touched by an intervening regulatory change

**Process:**

1. **Anchor on GRI 1 and work down** — State the candidate heading under GRI 1 (terms of the heading and the section / chapter notes). Only invoke GRI 2–6 if GRI 1 alone does not resolve the question, and name which GRI is doing the work (GRI 2(a) incomplete or unfinished, 2(b) mixtures, 3(a) most specific, 3(b) essential character, 3(c) heading occurring last, 4 most akin, 5 packaging, 6 subheading-level). The reasoning chain is what the broker will check; a confident heading without a stated GRI path is a flag, not an answer
2. **Build the candidate set** — Recommend the most likely 6-digit HS subheading and the destination 8- / 10- / 12-digit national line. If the question is genuinely ambiguous, present 2–3 candidate codes ranked by likelihood with the GRI rationale for each and the deciding fact a binding ruling would turn on. Do not present a single code with false confidence when the candidate set is real
3. **Score classification confidence** — High (eo nomine match, prior binding ruling on file, broker concurrence on a near-identical article), Medium (GRI 3(b) essential-character call or GRI 4 most-akin call with a defensible chain), Low (controlled-list-adjacent, parts-of versus articles-of-of dispute, novel commodity with no broker precedent — broker confirmation required before filing, binding ruling recommended if volume justifies). The confidence tier is non-decorative; it gates whether the company files the entry on this brief or holds for broker sign-off
4. **Build the duty stack** — General duty rate (column-1 / TARIC base rate) plus every additional applicable duty cited to its controlling instrument: Section 301 list and exclusion status, Section 232 steel / aluminium / derivatives, Section 201 safeguards, IEEPA executive-order tariffs (with the order number and the stacking rule that order specifies), Title VII AD / CVD case numbers and current cash deposit rates, EU additional duties, retaliatory duties on the lane. Pull every rate from the live source the broker uses on every classification — never embed a rate in the brief that lacks a citation, and never claim "0%" without naming the program that delivers the zero
5. **Test preferential programs** — For each program the company is considering: rule of origin (substantial transformation, tariff shift, regional value content, de minimis), record-keeping requirements (USMCA certificate of origin, supplier solicitation, EU REX, KORUS importer's certification), and the gap between what the company has and what the program requires. A claim without an origin analysis is the most common audit finding — the brief should not present a preferential rate without naming which rule of origin delivers it and what evidence is on file
6. **Compile the partner-government-agency and controls checklist** — Who else's filing is required: FDA prior-notice / facility registration for food, drug, medical device; USDA APHIS / FSIS for plant or animal-derived; FCC for RF-emitting; EPA TSCA chemical certification or pesticide registration; DOT for motor vehicles; CPSC for children's products; ATF for firearms / explosives; ITAR-listed defense articles; EAR-controlled with ECCN and license requirement; OFAC sanctioned-country end-user check; CITES for protected species. Call each one out with the controlling regulation and the document the broker needs in hand
7. **Specify marking and labeling** — Country-of-origin marking (19 USC 1304 in the US), 19 CFR 134 conspicuous / legible / permanent rules, FTC Made-in-USA standard if claimed, retail-package vs. ultimate-purchaser distinction, FPLA labeling if consumer-packaged, language requirements for the destination market
8. **Flag the audit and risk surface** — Recent CF-28 / CF-29 history on the lane, ADD / CVD scope-rulings risk if the article is adjacent to a covered line, transfer-pricing exposure if the related-party transaction value is the basis, first-sale-for-export eligibility if the supply chain is multi-tier, and any classification position the team is taking that the controlling text does not clearly support (these are the lines worth a binding-ruling request)
9. **Draft the broker hand-off note** — A short note in the company's broker voice: the recommended classification with the GRI chain, the confidence tier, the duty stack with citations, the preferential-program claim with the rule-of-origin evidence, the PGA filings required, the marking rule, the audit-risk callouts, and the question the broker is being asked to confirm. The broker note is the deliverable that closes the loop; the rest of the brief is the working file the question is built on
10. **Run the trust and accuracy check** — Before finalizing, scan the brief for: (a) any duty rate that lacks a citation to its controlling instrument, (b) any "0%" claim without a named program, (c) any GRI conclusion that did not state the GRI it relied on, (d) any preferential-program claim without a rule-of-origin analysis, (e) any controlled-list line ("not subject to EAR" / "not ITAR-listed") that is asserted rather than checked, (f) any binding-ruling reference whose ruling number cannot be cited. Flag rather than silently rewrite — confidently wrong duty stacks fund Customs penalties, not customer goodwill

**Output requirements:**

- A one-paragraph executive summary at the top: recommended classification(s), confidence tier, duty stack landed rate, controlling preferential-program claim if any, the gating broker hand-off
- A **Classification Recommendation** block — primary code with GRI chain, alternate codes with the deciding fact for each, confidence tier
- A **Duty Stack Table** — line items by authority (general / 301 / 232 / IEEPA / ADD / CVD / preferential program), each with citation and as-of date
- A **Preferential Program Analysis** — rule of origin, evidence on file, gap list, claimable / not-claimable / requires-confirmation flag
- A **PGA and Controls Checklist** — every agency that touches the article, controlling regulation, document the broker needs
- A **Marking and Labeling Block** — origin marking, conspicuous / legible / permanent rules, language requirements
- An **Audit-Risk and Position Notes** — CF-28 / CF-29 history, ADD / CVD scope risk, novel positions, recommended binding-ruling subjects
- A **Broker Hand-Off Note** — short, voice-aligned, ready to send to the broker of record
- A **Trust and Accuracy Check** subsection listing any item that did not pass the check, with the gating action (pull the citation, request the supplier methodology, recommend a binding-ruling request)
- Saved to `outputs/` if the user confirms, with the product identifier and the lane in the filename

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- The skill is deliberately a *broker hand-off*, not a Customs filing. The licensed broker is the final classification authority on every entry and the binding-ruling channel is the only way to lock a position in advance. Maintainers should not soften the broker hand-off framing — every operator who has tried to file directly off an LLM brief has rediscovered the binding-ruling channel the hard way
- The duty stack is intentionally cited rather than embedded. Section 301 lists, IEEPA executive orders, ADD / CVD cash-deposit rates, and FTA preferential rates move on a different cadence than this skill is updated; the brief reads from the broker's live source on every run. Any maintainer tempted to embed a "current rate" cache should instead invest the time in tightening the citation pattern
- The GRI-1-first reasoning chain is non-decorative. Most classification mistakes in 2026 broker reviews are GRI 3(b) essential-character calls that did not check whether GRI 1 already resolves the question. The brief enforces the order of operations the broker will enforce on review
- The "do not claim 0% without a named program" rule is doing load-bearing work. A 0% MFN rate exists only on a small set of headings; almost every other 0% the company sees is delivered by USMCA / KORUS / GSP / FTA / FTZ / bonded-warehouse / drawback / first-sale-for-export, each with its own documentation requirement. The brief makes the program explicit so the documentation is built in
- The interaction with `tariff-impact-mitigation-brief.md` is one-directional: the tariff brief consumes classification output, and unsettled classification questions surfaced in tariff-mitigation work route back here. Maintainers updating one should review the other only at the input / output contract — the analytical bodies are intentionally separate so each stays sharp on its own surface
