# USMCA Rules of Origin — Operator Reference

> **Posture:** This is a working operator's reference, not legal advice. Origin determinations, certifications of origin, and any move that relies on a binding ruling, an FTZ activation, or a drawback program registration belongs to the licensed broker and to counsel, not to a brief skill. The reference exists so the brief skills (`customs-classification-brief.md`, `tariff-impact-mitigation-brief.md`) and the operator team have a stable shorthand for what the agreement does and where the operator most often gets it wrong.

USMCA (United States–Mexico–Canada Agreement; T-MEC in Mexico; CUSMA in Canada) entered into force 2020-07-01. The agreement is up for its first Joint Review under Article 34.7 by 2026-07-01. The review is an extension-or-amend exercise, not a default-termination one — the trade-press consensus is extension with amendments, with automotive rules of origin, electric-vehicle / critical-minerals provisions, China-affiliated content restrictions, and labor-enforcement scope as the most-discussed amendment areas. The reference below frames the operator-side obligations under the agreement as it stands and flags the review-pressure areas as a separate section.

## Origin-Conferring Authorities, Not Tariff-Imposing

USMCA preference erases MFN duty on goods that meet the agreement's rule of origin (RoO) and that are entered with a valid certification of origin on file. Preference does **not** automatically erase Section 232, Section 301, or IEEPA add-ons; the interaction is determined by the controlling proclamation of each tariff authority. Cross-reference `knowledge-base/regulations/us-tariff-authorities-overview.md` for the U.S.-side stack.

The four origin-conferring rule families:

- **Wholly obtained or produced in the territory** — Goods entirely grown, mined, or produced in one or more of the three parties. Common for agricultural, mineral, and natural-resource goods
- **Tariff-shift rule (CTC — change in tariff classification)** — Goods produced in the territory from non-originating materials, where each non-originating material undergoes a specified change in classification. The shift required is product-specific (chapter, heading, or subheading) and is set out in the agreement's Annex 4-B Product-Specific Rules of Origin
- **Regional value content (RVC) rule** — Goods that meet a percentage threshold of regional value, calculated under one of the agreement's permitted methods (transaction-value method or net-cost method). The threshold is product-specific
- **Hybrid rule (tariff-shift plus RVC, or tariff-shift OR RVC)** — Many product-specific rules require the tariff shift *and* an RVC threshold, or allow either path. The operator picks the path the supply chain actually supports

The de minimis allowance lets a small percentage of non-originating materials (default 10% by value, lower or higher for specified goods) be ignored for origin purposes when the tariff-shift rule is the operative test. Textile and apparel goods have a separate de minimis regime by weight rather than value.

## Automotive Rules of Origin — The Highest-Stakes Sub-Regime

Passenger vehicles, light trucks, and heavy trucks face an additional, sector-specific rule set on top of the general RoO. The four certifications a vehicle producer files for preferential treatment:

- **Vehicle RVC certification** — The current threshold is 75% regional value content for passenger vehicles and light trucks (up from 62.5% under NAFTA). Heavy trucks have their own threshold. Phased implementation is in the agreement; the threshold the operator hits depends on the vehicle's production-year window
- **Labor Value Content (LVC) certification** — A specified percentage of the vehicle's content (40% for passenger vehicles, 45% for pickup trucks; the percentage on heavy trucks is its own number) must be produced by workers earning at or above the agreement's high-wage threshold (USD 16/hr at signing; the index mechanism is in the agreement)
- **Steel certification** — Specified percentage of steel by purchase value must be sourced North American with a "melted and poured" tightening that phased in
- **Aluminum certification** — Parallel to steel, with its own phased-in melted-and-poured tightening and percentage threshold

Producers of in-scope vehicles file all four certifications to claim USMCA preference on the vehicle. Producers of automotive parts file under the Annex 4-B Product-Specific Rules for the part in question.

## Certification of Origin — Format and Filer

USMCA replaced NAFTA's CBP Form 434 with a flexible, electronic-or-paper format. The certification can be made by **the importer, the exporter, or the producer**. The operator most often sees:

- **9 minimum data elements** — Importer / exporter / producer information, description of goods, HTS classification, origin criterion (A / B / C / D for the four rule families), blanket period (if applicable), authorized signature and date, and the certifier's role. The agreement's Annex 5-A lists the elements; CBP guidance pins the U.S.-side filer's posture
- **No prescribed form** — Any format the operator's system can generate is acceptable as long as it carries the 9 data elements. Many ERPs and TMS systems generate the certification as a structured PDF; broker-provided forms remain common
- **Blanket period of up to 12 months** — A blanket certification covers multiple shipments of the same goods within the period. The certifier is responsible for the blanket holding good for every covered shipment
- **Reasonable-care obligation on the importer** — The importer claims the preference and is on the hook for the certification's accuracy. CBP can request the certification on entry, post-entry, and during a verification within the agreement's recordkeeping window

## Recordkeeping and Verification

Importers must keep records supporting any preference claim for the period the agreement specifies (and CBP's general recordkeeping period; the longer applies). Records include the certification of origin, supporting documents that establish the origin determination (production records, supplier declarations, RVC calculations, LVC and steel / aluminum certifications on automotive), and entry-level documentation.

CBP verifies origin claims through written verification (questionnaire), site visits (with the producer's consent), and information-sharing under the agreement's verification procedures. A failed verification denies preference and back-bills the MFN duty plus any merchandise processing fee adjustment; the importer carries the back-bill regardless of which party signed the certification. Repeat or willful misclaims escalate into 1592 / 1592a penalty exposure.

## Common Operator Traps

- **Treating USMCA preference as a tariff-add-on eraser** — Preference does not automatically erase Section 232, Section 301, or IEEPA add-ons. The brief should always check the controlling proclamation of each authority before claiming preference erases the stack
- **Stale blanket on a substantially-changed product** — A blanket period of 12 months only holds if the goods have not changed in a way that changes the origin determination. A new supplier, a new component, or a shift in production location during the blanket period can break it; the certifier is responsible for re-issuing rather than letting the blanket ride
- **Treating de minimis as a universal back-stop** — De minimis is product-specific and rule-specific; it does not apply to every product or to the RVC test. Textiles and apparel use a different de minimis regime
- **Confusing the RVC method election** — Transaction-value and net-cost are different methods with different inclusions; the certifier picks one and documents the calculation. Many operators try to "spot-check" the result without picking a method, which fails verification
- **Filing automotive without all four certifications** — The vehicle RVC certification is necessary but not sufficient; LVC, steel, and aluminum each have their own filing and threshold. Missing any one denies preference on the vehicle even if the others pass
- **Importer-of-record on the certification but no documentation chain** — When the importer signs the certification, the importer is the certifier. The importer needs the supplier-declaration / production-record chain that supports the certification or the verification fails
- **Assuming the agreement extends automatically through the 2026 review** — Article 34.7 is a Joint Review with a sixteen-year extension only if all three parties extend. The trade-press consensus is extension-with-amendments, but the brief should not assume the post-2026 rule set is identical to the pre-2026 rule set

## 2026 Joint Review Pressure Areas

The Joint Review under Article 34.7 by 2026-07-01 is the most-discussed amendment cycle since the agreement entered force. Review-pressure areas the operator should expect to see resurface on the regulatory radar:

- **Automotive RVC tightening** — Industry advocacy has pushed the 75% threshold upward, with proposals for higher RVC, higher LVC, and tighter steel / aluminum melted-and-poured percentages. Phased implementation language makes the timing of any tightening a multi-year project for the producer
- **Electric-vehicle and critical-minerals provisions** — The agreement does not currently carry an EV-specific RoO regime; the review may add one, including provisions on critical-minerals sourcing and battery-cell production location. Critical-minerals supply chains today route through non-party countries; the review-side proposals tighten that
- **China-affiliated content restrictions** — Proposals to restrict content from non-party producers with specified ownership / control links surface in every review-cycle preview; the controlling-document language has not been settled
- **Labor enforcement scope** — Mexico-side facility-specific labor enforcement has been an active area under the existing agreement; the review may expand the scope or formalize the rapid-response mechanism
- **Cross-border trucking provisions** — B1 / B2 driver visa frameworks, English-language requirements, and trucking-permit harmonization surface in the review preview; any change here cascades into operator-side lane economics on Laredo, El Paso, and the cross-border O&D pairs

The operator's posture for the next 12 months: assume the agreement extends with amendments; track the controlling-document language as the review releases; do not bake the existing thresholds into a multi-year supplier-program decision without a sensitivity check against the review-pressure areas.

## How the Brief Skills Should Use This Reference

- **`customs-classification-brief.md`** — When the brief is producing an HTS classification on a North-American-origin good, the brief should pin the USMCA rule family that applies (CTC, RVC, hybrid, wholly obtained), the controlling product-specific rule from Annex 4-B, and the certification-of-origin filer (importer / exporter / producer) the entry will rely on. The brief does not produce the certification — that is broker / counsel work — but the brief's classification must be the one the certification carries
- **`tariff-impact-mitigation-brief.md`** — When the brief is producing a U.S.-side mitigation analysis on a good that has a credible USMCA preference path, the brief should treat USMCA preference as a candidate move with its own evidence chain (rule-of-origin, certification, documentation chain, recordkeeping). The brief should never claim USMCA preference erases a Section 232 / Section 301 / IEEPA add-on without pinning the controlling proclamation; the overlap is authority-specific and the brief should require the document language
- **`compliance-document-checker.md`** — When the brief is reviewing a USMCA certification of origin, the brief should run the 9-minimum-data-element checklist, the blanket-period staleness check, the certifier-role consistency check (the named certifier matches the role they actually played), and the supporting-documentation pointer (does the operator hold the chain that would survive a CBP verification, regardless of which party signed)
- **All briefs** — Pin every authority cited; sanity-check interactions with U.S.-side and EU-side regimes (cross-reference `us-tariff-authorities-overview.md` and `eu-trade-regulations-overview.md`); flag the 2026 Joint Review as a stated assumption the operator should re-check against the controlling-document language before any post-2026 commitment

## Source-of-Truth Pointers

The agreement text and the U.S.-side implementing regulations are the controlling documents; trade-press summaries inform timing and review-pressure framing but should never substitute for the controlling text on a brief. Pin per-citation:

- USMCA / T-MEC / CUSMA agreement text and annexes (Annex 4-B Product-Specific Rules of Origin; Annex 5-A Certification of Origin Minimum Data Elements; Annex 6-A Vehicle Producer Certifications)
- 19 CFR Part 182 (U.S. implementing regulations on origin and verification)
- CBP Informed Compliance Publications and Frequently Asked Questions on USMCA
- Article 34.7 (Joint Review) and the Federal Register or USTR notice for any review-cycle amendment language as it issues
- Mexico's Diario Oficial de la Federación and SAT guidance on T-MEC for the Mexico-side filer obligations
- Canada's CBSA D-Memoranda on CUSMA for the Canada-side filer obligations

## Out of Scope

This reference is the U.S.-side operator's working shorthand on USMCA. It does **not** cover:

- Mexico-side IMMEX / Maquiladora program tax treatment, which is a Mexico-side regime adjacent to but not part of the agreement
- Canada-side CUSMA-equivalent procedural specifics where they diverge from the U.S.-side filer posture
- USMCA Chapter 23 (Labor) facility-specific enforcement procedures, which sit in their own regulatory area
- Sanctions screening, dual-use controls, and forced-labor / UFLPA enforcement, which sit in their own KB area
- Specific tariff rates, MPF / HMF amounts, drawback program registration mechanics, and FTZ activation — these are live values pulled from source documents on every brief run

## Maintainer Note

This is the third content file in `knowledge-base/regulations/`, completing the U.S. / EU / North-American regional triad with `us-tariff-authorities-overview.md` and `eu-trade-regulations-overview.md`. The same reference posture applies on rewrites: scope rule + obligation + common trap + source-of-truth pointer per regime, no embedded live rates / thresholds / scope lists, route classification work to `customs-classification-brief.md`. Sibling files flagged for future build when an operator workflow needs them: `uk-trade-regulations-overview.md` (UK post-Brexit GSP / DCTS, UKCA, CDS), `china-export-controls-overview.md` (BIS EAR, OFAC SDN, UFLPA-adjacent forced-labor enforcement). The UK and China files would each cover their own regime independently; do not expand this overview to include them.
