# EU Trade Regulations — Reference Overview

Short reference consumed by `skills/admin/cbam-embedded-emissions-compliance-brief.md`, by `skills/admin/customs-classification-brief.md` when the operator's lane mix touches EU import / export, and by any future EU-side compliance skill the repo grows. It describes the active EU-trade authorities the operator has to read together in the 2026 environment. Not a substitute for outside trade counsel, for the company's licensed customs agent in the relevant member state, or for a DPA / verifier engagement on a binding question.

## Why this overview exists

Through 2025 and into 2026, the EU's trade-and-import compliance stack moved from a set of comparatively independent regimes into an operationally interlocked one. A single EU import line can now carry an MFN duty under the Common Customs Tariff, a CBAM certificate exposure on embedded emissions, an EUDR due-diligence requirement on commodity traceability, an ICS2-version-3 advance-data filing through the carrier, an EU Customs Single Window cross-reference, plus the usual VAT and Authorized Economic Operator (AEO) considerations — with each regime having its own scope rule, its own filing channel, its own deadline, and its own non-compliance consequence.

The mitigation and compliance brief skills need a stable shorthand for what each regime does so the operator does not have to re-learn the framework on every regulatory event. Live rates, threshold values, and active scope lists are intentionally **not** captured here — those churn and a cached value silently rots; the skills should pull live values from the source documents on every run.

## The regimes at a glance

### CBAM (Carbon Border Adjustment Mechanism)

- Regulation (EU) 2023/956 establishing the CBAM, with subsequent Implementing Regulations and the Omnibus simplification round affecting authorization and reporting cadence into the definitive phase
- Goods scope (definitive phase): cement, iron and steel, aluminium, fertilisers, electricity, hydrogen, plus specified precursors and downstream derivative products listed in the Annex
- Operator obligation: importers above the relevant mass threshold must hold *Authorized CBAM Declarant* status (directly or via an indirect customs representative), report embedded direct and (where in scope) indirect emissions per CN line on an annual basis, surrender CBAM certificates corresponding to the declared embedded emissions less any verified carbon price already paid in the country of origin
- Data quality: emissions data should be installation-specific and verified by an accredited verifier; default values are permitted as a fallback under conditions and caps that have been adjusted in the Omnibus simplification round and may be adjusted again
- Common traps: assuming an FTA preference removes CBAM exposure (it does not — CBAM is a separate mechanism); assuming a re-export from the EU eliminates the CBAM line (the inward-processing and re-export interactions are detailed and worth a broker confirmation); assuming the precursor scope is stable (the Annex evolves)
- Source-of-truth pointers: the EU CBAM Registry guidance, the latest Implementing Regulation, the most recent Omnibus text, the company's member-state competent authority advisory feed

### ICS2 (Import Control System 2)

- The EU's enhanced advance cargo information system. Carriers (or their representatives) submit a complete Entry Summary Declaration (ENS) before goods enter or transit the EU, against the ICS2 platform
- 2026 milestone: the version-3 release replaces the prior message formats; once the cutover date passes, the prior format is rejected. Enforcement extends across all transport modes by mid-2026
- Operator obligation: while the ENS is the carrier's filing, the importer is the source of much of the data the carrier must transmit (HS6 minimum granularity, consignor / consignee, goods description, value where required). Inadequate or late upstream data is a recurring source of carrier-side ENS rejections that delay arrival
- Common traps: sending HS6 only on lines where the carrier needs HS8 to clear ICS2's risk model; counting on a generic goods description that the platform's risk-screening flags; assuming non-EU transshipment legs do not feed the ENS (some do)
- Source-of-truth pointers: the European Commission's ICS2 program documentation, the company's freight forwarders' and carriers' ICS2 readiness notices, the member-state customs authority advisories

### EUDR (EU Deforestation Regulation)

- Regulation (EU) 2023/1115 on deforestation-free supply chains. Covered commodities: cattle, cocoa, coffee, palm oil, soy, rubber, wood, plus derived products including chocolate, leather, furniture, paper, certain rubber goods
- Operator obligation: covered importers and traders perform a documented due-diligence process, collect geolocation data on the production plot of origin, assess deforestation risk against the EU's risk-classification categorization for the source country, and submit a digital Due Diligence Statement (DDS) into the Commission's Information System before the goods are released for free circulation
- Compliance windows: large and medium operators must comply by the end of 2026; small enterprises have a later deadline. Member-state competent authorities run risk-based controls
- Common traps: treating the geolocation data as a one-time collection (it is per-batch); confusing supply-chain certifications (RSPO, FSC, etc.) with the EUDR DDS (certifications can support the due-diligence narrative but do not substitute for the statement); under-scoping derived products
- Source-of-truth pointers: the Commission's EUDR Information System guidance, the country-risk classification, the latest delegated and implementing acts, the company's competent-authority advisories

### EU Customs Code and the Customs Single Window (CSW-CERTEX)

- The Union Customs Code (Regulation (EU) No 952/2013) and its Implementing and Delegated Regulations remain the foundation: customs status, customs procedures (release for free circulation, inward processing, customs warehousing, end-use, transit, export), customs decisions, AEO status
- The EU Customs Single Window environment, including CSW-CERTEX, interconnects national customs systems with non-customs authorities (CBAM Registry, EUDR Information System, sanitary / phytosanitary controls, market-surveillance) so a single declaration can be cross-checked against the underlying authorizations
- Operator obligation: data quality and consistency across the underlying systems matter more than they did in the pre-CSW-CERTEX environment — a CN code that is correct in the customs declaration but inconsistent with the EUDR DDS or the CBAM declaration triggers a control
- Source-of-truth pointers: the Union Customs Code consolidated text, the CSW-CERTEX program documentation, the company's customs broker integration documentation

### EU VAT, Import VAT, and the IOSS / OSS regimes

- Import VAT is due on most non-EU-origin goods imported for free circulation, with collection mechanics varying by member state and by import value. The IOSS (Import One Stop Shop) regime applies to certain low-value B2C consignments
- Operator obligation: register and report under the appropriate regime, deduct input VAT where eligible, manage the postponed-accounting election where the member state offers it
- Common traps: confusing customs duty with import VAT; under-declaring transaction value to fall under an IOSS threshold (a documentation-quality issue, not a tax-planning move); missing the input-VAT deduction window
- Source-of-truth pointers: the relevant member state's tax authority guidance; the EU VAT Directive consolidated text

### Authorized Economic Operator (AEO)

- Status that recognizes a trader's customs compliance, financial solvency, practical standards of competence, and (for AEOS) safety and security standards. Confers procedural simplifications, reduced control rates, and certain mutual-recognition benefits with non-EU AEO equivalents
- Operator obligation: maintain the underlying compliance program; respond to the periodic re-evaluation. AEO status interacts favorably with ICS2 risk scoring and with CBAM authorization
- Source-of-truth pointers: the EU AEO program documentation; the company's audit-trail records

### Sanctions and dual-use export controls

- EU sanctions regimes (country-targeted, sectoral, person- and entity-targeted) and the dual-use export-control regime (Regulation (EU) 2021/821) cover what the operator can ship to whom, with whom the operator can transact, and what financial flows are permitted
- Operator obligation: screening of counterparties, screening of end-use and end-user, license application where required. Sanctions-screening is a separate compliance function and is typically not a tariff-mitigation conversation
- Common traps: thinking re-export from a third country to a sanctioned destination is outside scope (it is generally not, where the goods are EU-origin or the operator is an EU person); thinking dual-use is a small product list (it is not — the Annex is broad and updated)
- Source-of-truth pointers: the EU sanctions map, the consolidated dual-use list, the member-state licensing authority

### EU Emissions Trading System (EU ETS) — relevant only as a CBAM cost anchor

- The CBAM certificate price is linked to the weekly EU ETS auction average. The operator does not transact directly in EU ETS allowances by virtue of CBAM exposure, but the certificate-volume × certificate-price math is the live financial-exposure number
- Source-of-truth pointer: the Commission's published CBAM certificate price feed

## How the brief skills should use this reference

The brief skills should:

1. Pin every regulation cited in the analysis to the section of this overview, with a one-line callout of which controlling document (Regulation, Implementing Regulation, Omnibus text, member-state advisory, Commission guidance) supplies the live scope and value
2. Sanity-check interactions — if the analysis claims a move (FTA preference, AEO simplification, IOSS election) erases or substitutes for an exposure, the brief should require the controlling-document language that supports that claim, not infer it from the press summary
3. Flag the common traps explicitly: CBAM goods-scope drift; ICS2 data granularity gaps; EUDR per-batch geolocation requirement; CSW-CERTEX cross-system inconsistencies; sanctions-and-dual-use scope misreads
4. Route classification questions to `customs-classification-brief.md` rather than re-doing the work — the EU Common Customs Tariff classification is the same operator workflow as the U.S. HTS classification covered there

## Scope limits

This overview is a working operator's reference, not legal advice. It does not cover EU competition / antitrust, member-state-specific corporate-law obligations, member-state environmental regimes outside the CBAM / EUDR scope captured above, third-country sanctions regimes, or the U.K. (which since the Withdrawal has its own evolving framework — a `uk-trade-regulations-overview.md` belongs in this directory when an operator workflow needs it). Any move that depends on a binding tariff information (BTI), a binding origin information (BOI), an AEO authorization, an IOSS registration, an EUDR risk re-categorization, or a sanctions license belongs to the broker and to counsel, not to a skill.

## Maintainer notes

- Live rates, threshold values, certificate prices, country-risk categorizations, and active scope lists are deliberately **not** captured here. Every brief must pull those values from the controlling source documents in the brief's input. A cached threshold in this file would silently rot; a missing threshold forces the operator to look at the source
- The Omnibus simplification round affecting CBAM authorization-window dates and certain default-value treatments is the kind of change that warrants a one-paragraph update at the top of the affected section here, with the date of the update. It is not a reason to rewrite the section
- A future `usmca-rules-of-origin.md`, `uk-trade-regulations-overview.md`, or `china-export-controls-overview.md` belongs alongside this file rather than as expansions of this overview. Keeping each regime's overview in its own file preserves the brief skills' ability to load only what they need
