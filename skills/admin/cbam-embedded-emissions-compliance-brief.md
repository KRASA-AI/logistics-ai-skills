---
name: "CBAM Embedded Emissions Compliance Brief"
category: admin
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~75 min/EU-import program update"
version: 1.0
last_eval_score: null
---

# 🇪🇺 CBAM Embedded Emissions Compliance Brief

## Purpose

Take the company's EU-import portfolio of CBAM-covered goods — cement, iron and steel, aluminium, fertilisers, electricity, hydrogen, and any in-scope precursors — and produce the working brief the operator needs to clear the CBAM definitive phase: confirm authorized declarant posture, score the supplier-by-supplier embedded-emissions data the company actually has versus what it still needs, lay out the annual declaration calendar, and draft the supplier-outreach and customer pass-through notes the next 60–90 days require. Built to be re-run when the import portfolio changes, when a covered scope expansion is announced, or when supplier emissions data is refreshed.

## When to Use

Use this skill when the company imports any CBAM-covered good into the EU above the de minimis mass threshold, OR when finance / sustainability / customs asks "are we ready for the definitive-phase declaration" on the calendar period now in progress, OR when a non-EU supplier sends through a new emissions data package the team needs to validate before booking it into the CBAM workflow. Trigger it again at every material change — a goods-scope extension, a verifier accreditation change, an Omnibus simplification update, a default-value re-publication, a CN-code reclassification on the company's import portfolio — so the brief is always anchored to the current legal text and the current data set rather than the press summary or last quarter's snapshot.

This skill is for *EU-import CBAM exposure*. For U.S. tariff actions (Section 301 / 232 / IEEPA), use `tariff-impact-mitigation-brief.md` — the analytical pattern is parallel but the legal anchors and the operator workflow are different. For per-shipment HTS / CN classification, use `customs-classification-brief.md` — this brief consumes its outputs and routes new classification work back to it. For the supplier-relationship side of an emissions data ask, the supplier-outreach draft this brief produces is intentionally short and replaceable; deeper supplier engagement belongs in procurement, not in a one-off note.

## Required Input

Provide the following:

1. **Authorization posture** — Whether the company holds *Authorized CBAM Declarant* status, the application date if pending, the indirect customs representative arrangement if used, the member-state competent authority of record, and the EORI on file. Note any in-flight authorization-extension or Omnibus-window position the team is relying on
2. **Import portfolio (CBAM goods only)** — Last 12 months of EU entries plus the next 12 months of forecast volume by CN code, country of origin, supplier (installation level where known), port of entry, customs procedure (release for free circulation vs. inward processing vs. customs warehouse), declared transaction value, and the mass tonnage that drives the threshold and the embedded-emissions math
3. **Embedded-emissions data on file** — Per supplier and per installation: the emissions data package the supplier has provided (direct emissions, indirect emissions, system boundary, calculation methodology, reporting period, accredited verifier and verification statement). Note for each line whether the data is verified, awaiting verification, draft / unverified, or absent (in which case the team has been using a default value)
4. **Default-value usage tracker** — Which CN codes, suppliers, and installations are currently being declared on default values, the cap and conditions under which the default is permitted, and the date by which actual data is expected
5. **Customer / contract context** — For imports tied to specific EU customers (especially in pass-through arrangements with downstream manufacturers who are themselves CBAM-exposed on derivative products), the contract pass-through stance for any CBAM cost, the customer's own CBAM declarant posture, and any joint supplier-engagement that is already in motion
6. **Calendar position** — The current declaration window the team is preparing for (calendar year of imports, declaration submission deadline of record, certificate-purchase window, surrender / true-up date), plus any internal financial-close deadline that runs ahead of the regulatory date
7. **Decision authority and budget** — Who at the company can authorize a supplier-data project budget, who signs the annual declaration, and which dollar/euro thresholds escalate the discussion to finance and to the sustainability function

## Instructions

You are a customs and sustainability operations specialist's AI assistant working a CBAM compliance cycle. Your job is to convert a noisy supplier-data set and a moving regulatory text into a brief that names the gaps, ranks the actions, and produces the supplier and customer comms the next 60–90 days require. You are not legal counsel and you are not the verifier — call out where the analysis hands off to each.

**Before you start:**

- Load `config.yml` for company entity, EORI, member state of authorization, designated competent authority contact, voice spec for supplier-facing and customer-facing correspondence, and any organization-wide sustainability-disclosure positions (CSRD, voluntary reporting frameworks) that the CBAM data feeds
- Reference `knowledge-base/regulations/eu-trade-regulations-overview.md` for the CBAM authority anchor, the CN-scope rule, the relationship between CBAM, ICS2 advance-data filings, EUDR due-diligence statements, and the EU Customs Single Window. The overview also points to the live source documents (Implementing Regulations, the Commission's CBAM Registry guidance, the latest Omnibus simplification text) the brief should pin its analysis to rather than rely on a cached rate or threshold
- Reference `knowledge-base/regulations/us-tariff-authorities-overview.md` only where the company's lane mix has both EU-import and U.S.-import exposure that interacts (e.g., a steel product that carries U.S. Section 232 and EU CBAM on opposite legs of the same supplier program)
- Reference `knowledge-base/best-practices/` for any company-specific data-quality rules the sustainability function has set on accepted supplier methodologies (e.g., "GHG Protocol Product Standard", "ISO 14067", "supplier-specific verified")
- If a previous CBAM brief lives in `outputs/`, load it to spot what has actually moved (new supplier data verified, gap closed, default-value reduction) versus what is still outstanding

**Process:**

1. **Confirm declarant posture** — Open with a one-paragraph status block: authorization (held / pending / not-yet-applied), member state, indirect customs representative arrangement if any, the regulatory window the brief is sized to, and the single-paragraph "what changes if we do nothing" call. If authorization is pending, name the controlling deadline and the gating action
2. **Score the import portfolio against the goods scope** — Build a portfolio table: CN code, country of origin, installation, supplier, last-12-months tonnage, next-12-months forecast tonnage, embedded-emissions tCO2e (best estimate using either supplier data or the default), and the data-quality flag (verified / pending verification / unverified / default). Sort by tCO2e descending — that is the order in which supplier-engagement effort returns the most reduction in declared-emissions risk
3. **Identify the goods-scope edge cases** — Flag any CN code that has moved into or out of scope in the most recent regulatory update, any precursor / derivative line where the embedded-emissions methodology is unsettled, and any line where the team is treating an article as out-of-scope on a position the controlling text would not clearly support. These are the lines worth a broker confirmation before the next declaration filing
4. **Build the supplier-data gap table** — For every supplier in the portfolio, what data the team has, what is missing, the verification status, the deadline by which the data is needed for the next declaration window, and the proposed escalation path (request, follow-up, switch to default with cost note, supplier change). Treat the supplier with the largest tCO2e exposure and the weakest data as the highest-priority gap, even if a smaller supplier is technically more overdue
5. **Project the certificate exposure** — Translate declared tCO2e into the certificate-volume projection for the upcoming surrender window using the EU ETS-linked weekly average price the team is tracking (do not embed a price here; pull from the source-of-truth feed the finance team uses on every run). Show the high / mid / low scenarios driven by data-quality outcomes (full verified data set vs. mixed vs. heavy default-value reliance), so finance sees the dollar-or-euro spread the supplier-data project is actually closing
6. **Lay out the calendar** — A working calendar from today through the next surrender date: data-collection cutoff for each supplier, internal verification review, member-state declaration submission, certificate-purchase windows, surrender date, plus the company's own financial-close hand-offs. Mark every external-dependency date in a way that makes the gating party explicit
7. **Draft the supplier-outreach note set** — One short note per supplier needing data, in the company's voice: what the company needs (with the data fields named, not the regulation cited), why (one line referencing the EU regulation so the supplier sees the binding driver), the deadline, what the company will do if data is not received (default-value declaration with cost implications that may flow back through the commercial relationship), and the named contact at the company. Voice is collaborative, not punitive — most suppliers are also navigating this for the first time
8. **Draft the customer pass-through note where applicable** — For EU customers in contracts with a CBAM cost pass-through, a short note that names the in-scope products, the certificate-cost basis, the timing of when the cost will be invoiced, and the supporting documentation the company will provide on request. Do not commit to a fixed cost; the certificate price moves and the supplier data moves
9. **Draft the internal finance memo** — One-page memo to the CFO and head of sustainability: portfolio exposure today, projected exposure at the next surrender, the supplier-data investments that move the projection, the decision points in the calendar that need approval, and the sensitivity to certificate price. Frame the supplier-data project as a margin defense, not a sustainability initiative — finance will fund the margin defense
10. **Recurrence and trigger plan** — Re-run cadence (quarterly default for portfolio review; immediately on any goods-scope or methodology update, on any new supplier added, on any verifier-accreditation change). Name the upstream feeds that should trigger an off-cycle re-run (CBAM Registry guidance updates, Implementing Regulation amendments, Omnibus revisions, member-state advisories, broker bulletins)
11. **Run the trust and accuracy check** — Before finalizing, scan the brief for: (a) any embedded-emissions number that lacks a cited source (supplier package, default-value table, prior verified declaration), (b) any goods-scope assertion that was not pinned to the controlling regulation citation, (c) any "verified" claim where the verifier accreditation cannot be traced, (d) any pass-through commitment that exceeds what the contract actually allows, (e) any deadline that is sourced from a press summary rather than from the regulation. Flag rather than silently rewrite — a confidently wrong CBAM number breaks trust with both finance and the customer

**Output requirements:**

- A one-paragraph executive summary at the top: authorization posture, in-scope portfolio tonnage, projected certificate exposure (with high/mid/low), the top three supplier gaps, the next gating regulatory date
- A **Declarant Posture** block — authorization status, IDR arrangement, member-state competent authority of record
- A **Portfolio Exposure Table** — CN code, country, installation, tonnage, tCO2e, data-quality flag, ranked by tCO2e descending
- A **Goods-Scope Edge Cases** block — recent in/out moves, unsettled methodology lines, positions worth a broker confirmation
- A **Supplier-Data Gap Table** — supplier, data status, deadline, escalation path, ranked by tCO2e × data-weakness
- A **Certificate-Volume Projection** — high / mid / low scenarios with the assumption set, no embedded price
- A **Calendar Block** — data-collection, verification review, declaration submission, certificate purchase, surrender, with the gating party named on every external dependency
- A **Supplier-Outreach Note Set** — one note per supplier
- A **Customer Pass-Through Note Set** — one note per applicable customer
- A **Finance Memo** — one page for CFO and sustainability head, framed as margin defense
- A **Recurrence Plan** — re-run cadence and the upstream-feed triggers
- A **Trust and Accuracy Check** subsection listing any item that did not pass the check, with the gating action (pull the verifier statement, request the supplier methodology, confirm the CN scope with the broker)
- Saved to `outputs/` if the user confirms, with the declaration-window identifier in the filename

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- The CBAM definitive phase is the operative reality for any operator with EU-import exposure on the in-scope CN codes. Through the transitional period the brief would have framed the work as preparation; in the definitive phase the work is recurring compliance, and the cadence reflects that — the recurrence section is not optional. If a future Omnibus revision pushes the surrender or declaration date, update the calendar template here, not the supplier-outreach template, which is timing-agnostic
- Default values are a legitimate fallback, not a permanent posture. The brief's framing — that supplier-data investment is margin defense — is what gets the finance budget that closes the gap. Maintainers should not soften that framing in the finance memo template; it is doing the load-bearing work
- The verification step is intentionally light in this skill — the brief identifies whether verifier-statement evidence exists, but the actual verifier engagement and the methodology audit belong to the sustainability function and the accredited third party. A future skill could be the verifier-readiness checklist; for now, the gap table is sufficient
- The interaction between CBAM and downstream EU sustainability disclosure (CSRD, voluntary reporting) is real but out of scope for this brief. Where the team's CBAM data feeds CSRD scope-3 reporting, surface that as a one-line note in the finance memo and let the disclosure team consume the data set the brief produces
- The brief deliberately does not draft the formal CBAM Registry submission. That submission is a structured filing into the EU CBAM Registry through the member-state competent authority and belongs to the customs broker / declarant team operating the registry credentials. The brief produces the working data set and the supplier and customer comms; the registry filing is a downstream step
