# U.S. Tariff Authorities — Reference Overview

Short reference consumed by `skills/admin/tariff-impact-mitigation-brief.md` and `skills/admin/customs-classification-brief.md`. It describes the statutory authorities the brief skills cite when sizing exposure and ranking mitigation moves. Not a substitute for outside trade counsel or for the company's licensed customs broker on a binding ruling.

## Why this overview exists

Through 2025 and into 2026, U.S. tariff policy moved from a handful of slow-moving programs to a stack of overlapping authorities the operator has to read together. A single inbound HTS line can carry MFN duty, a Section 301 add-on, a Section 232 add-on, an IEEPA executive-order add-on, an antidumping or countervailing duty, plus quota or safeguard treatment, with each authority having its own scope rule, exclusion process, and effective-date mechanic. The mitigation brief needs a stable shorthand for what each authority does so the operator does not have to re-learn the framework on every tariff event.

The goal of this reference is to give the tariff and classification skills a consistent vocabulary and a consistent pointer to the controlling text — not to embed live rate tables (which churn) or specific exclusion lists (which the team should pull from the source on every run).

## The authorities at a glance

### Section 301 (Trade Act of 1974)

- USTR-administered remedy for foreign acts, policies, or practices that are unreasonable or discriminatory and burden U.S. commerce. The operator most often sees this as the China-origin tariff lists (List 1, 2, 3, 4A, 4B in the original action; subsequent modifications and statutory four-year reviews reshape the lists)
- Scope is by HTS subheading and country of origin. Goods that fall in scope when entered for consumption pay the Section 301 add-on rate on top of the MFN duty
- Exclusion process: USTR has from time to time opened exclusion windows; granted exclusions are HTS-specific and time-limited. The brief should always check the active USTR exclusion docket rather than rely on a cached list
- FTZ treatment matters: goods admitted to an FTZ in privileged-foreign status before the effective date generally retain pre-action treatment; goods admitted as non-privileged-foreign or after the effective date pay the new rate on entry into U.S. commerce. Drawback eligibility on Section 301 has shifted over time and is the kind of question that warrants a broker confirmation rather than a skill-level assertion

### Section 232 (Trade Expansion Act of 1962)

- Commerce-led national-security investigation; tariff or quota remedies imposed by presidential proclamation. The operator most often sees this on steel, aluminum, derivative steel and aluminum products, and from time to time on autos and parts, semiconductors, or critical minerals depending on the active proclamations
- Scope is by HTS subheading and country of origin. Country-specific exemptions, tariff-rate quotas, and global versus origin-specific treatment have changed multiple times — the controlling proclamation and the latest CBP CSMS message are the sources to cite
- Derivative-product expansions are a recurring trap: the operator can be importing a finished product not on the original list, but a derivative-list expansion brings it into scope mid-program. The brief should always check whether a derivative expansion has been issued since the last cached run

### Section 201 (Trade Act of 1974)

- ITC-investigated safeguard, presidential proclamation. Less frequently active than 301 or 232, but when it is (washing machines, solar cells / modules historically), it imposes a tariff-rate quota or an absolute quota with annual liberalization. Country exclusions and the over-quota / in-quota rate split are both relevant

### IEEPA (International Emergency Economic Powers Act)

- Executive-order tariff authority used in 2025 and 2026 against multiple trading partners. Scope, rate, and effective-date treatment have varied executive order to executive order, which is exactly why the brief should pin scope to the specific EO text rather than a press summary
- Court challenges to IEEPA-tariff use have been in flight; the operator should plan to the operative date rather than the announced date, and should track injunctions through the broker bulletin and the CSMS feed
- IEEPA tariffs can stack on top of Section 301 / 232 and on top of MFN duty depending on the EO. Stacking is the single most common mistake in non-broker exposure math

### Antidumping and Countervailing Duties (Title VII, Tariff Act of 1930)

- Commerce determines dumping margin or subsidy rate; ITC determines material injury; orders are HTS-and-producer specific, with rates that are reviewed annually (administrative review) and that can be retroactively assessed on entries within the period of review
- The operator's exposure on AD/CVD is materially different from Section 301 / 232 because final liability is set at liquidation, often years after entry. Cash-deposit rates at entry are not final. This timing reality should be flagged in any mitigation brief that touches an AD/CVD-covered HTS

### USMCA and other FTAs

- Origin-conferring rules, not tariff-imposing rules. USMCA preference can erase MFN duty when the rule of origin (tariff-shift, regional value content, or specific process) is met and a valid certification is on file. USMCA preference does **not** automatically erase Section 232 or Section 301 add-ons; the interaction depends on the proclamation
- The operator should not assume an FTA preference removes IEEPA-tariff exposure either; recent EO text has been variable on this point. Always check the controlling EO

### Miscellaneous Tariff Bill (MTB), GSP, and other programs

- MTB is a periodic congressional act granting temporary duty suspensions or reductions on specified products. Active windows are statutory — when the program is lapsed, the suspension does not apply
- GSP (Generalized System of Preferences) lapses and renews on its own schedule. When lapsed, retroactive refund is sometimes provided when the program is reauthorized; when active, the country-eligibility list and the product-eligibility list both matter

## The mitigation menu, structured

The mitigation brief evaluates moves against this menu. Each move is real, but each has a feasibility window and a program prerequisite that the brief should check rather than assume.

### Duty deferral and elimination

- **Foreign Trade Zone (FTZ)** — Activated zones admit foreign merchandise without duty until entered for consumption. Re-export from the zone avoids U.S. duty entirely. Weekly entry treatment can compress MPF. Privileged-foreign status locks classification and rate at admission, which is sometimes valuable around a tariff-effective date and sometimes not
- **Bonded warehouse (Class 3 / 4 / 7)** — Defers duty up to five years; useful for timing, less useful for rate avoidance unless a known rate change is coming
- **Duty drawback** — Refund of duty on imported merchandise that is subsequently exported (1313(j)(1) unused-merchandise direct-identification, 1313(j)(2) substitution unused, 1313(p) petroleum, 1313(q) wine; manufactured-merchandise drawback under 1313(a) and (b)). Eligibility for drawback on Section 301, Section 232, and IEEPA tariffs has been treated differently across actions — always confirm with the broker against the controlling text

### Valuation moves

- **First-sale-for-export** — Lower dutiable value where a multi-tier sale exists and statutory conditions are met. Requires evidentiary support that survives CBP scrutiny: the first-sale transaction must be a bona fide sale destined for export to the U.S., at arm's length, with adequate documentation. Not a paper-only restructure
- **Unbundling non-dutiable charges** — International freight, insurance, and certain post-importation services and royalties may be excluded from transaction value when properly documented and separately stated. Common audit area; the brief should treat any "unbundling" claim as requiring documentary support, not just an invoice line split

### Origin and classification moves

- **Substantial transformation / re-source** — Shifting production to a non-affected country can change the country of origin and remove the action's scope. Real moves take months to years and depend on the product's production-step sensitivity
- **Tariff engineering** — Legitimate product modification that shifts the classification to a different HTS at a lower rate. Bona fide engineering is recognized by CBP; sham modification is not. This belongs in `customs-classification-brief.md`, not the mitigation brief
- **Component-vs-finished split** — Importing components subject to a lower or no tariff, with assembly in the U.S. or in an FTZ. Has working-capital and labor-cost implications that the brief should surface, not gloss

### Procedural and timing moves

- **Exclusion request** — When an exclusion process is open (USTR Section 301; Commerce 232 derivative-product process), file with product specificity and economic justification. Outcomes are HTS-specific and time-limited
- **Pull-forward / hold-back of entries** — Bracketing an effective date is sometimes legitimate (transit on the water before effective date is treated under pre-action rate where the EO so provides), and is sometimes not (artificial entry-date manipulation is not). The brief should describe the rule under the controlling text, not propose a workaround that reads as evasion
- **Bonded vs. consumption entry on the same shipment** — Bonded admission to wait out a known short-window tariff event has cash-flow merit when warehouse fees are less than the duty exposure for the wait period

### Pricing and contract moves

- **Pass-through to the customer** — Structural decision driven by the contract's pass-through stance, not by the brief. The brief frames the conversation; the rate change is finalized in the company's pricing system

## How the mitigation brief should use this reference

The brief should:

1. Pin every authority cited in the analysis to the section of this overview, with a one-line callout of which controlling document (EO, proclamation, USTR notice, CBP CSMS) supplies the live rate and scope
2. Sanity-check stacking — if the analysis claims a move (e.g., USMCA preference, drawback) erases an authority's add-on, the brief should require the controlling-document language that supports that claim, not infer it
3. Flag the common traps explicitly: derivative-list expansion under 232; AD/CVD final-liability timing; IEEPA scope and stacking variability; exclusion-list expiration; FTZ status election timing
4. Route HTS-classification questions to `customs-classification-brief.md` rather than re-doing the work

## Scope limits

This overview is a working operator's reference, not legal advice. It does not cover state tax treatment, Buy America / Buy American Act procurement rules, end-use programs (TIB, Chapter 98), foreign tariff regimes, or sanctions screening (which sits in its own KB area). Any move that depends on a binding ruling, an FTZ activation, a drawback program registration, or a USMCA certification belongs to the broker and to counsel, not to this skill.

## Maintainer notes

- Live rates and active exclusion lists are deliberately **not** captured here. Every tariff brief must pull those values from the controlling source documents in the brief's input. A cached rate in this file would silently rot; a missing rate forces the operator to look at the source
- Recent IEEPA-tariff developments and any court action on them should be tracked through the broker bulletin and CSMS rather than embedded here. When a major reframing happens (a new statutory authority, a structural change in stacking treatment), this overview should be updated with one paragraph and the date of the update at the top of the changed section
- Country-of-origin rules under USMCA and other FTAs are well-covered elsewhere; this file deliberately does not reproduce them. Add a `usmca-rules-of-origin.md` to `knowledge-base/regulations/` when an operator workflow needs it, rather than expanding this overview
