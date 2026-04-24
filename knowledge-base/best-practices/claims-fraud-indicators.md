# Claims Fraud Indicators — Reference Addendum

Short reference consumed by `skills/admin/claims-documentation-builder.md` and by the claims-review step of the `skills/admin/carrier-fraud-screening-brief.md`. It describes the patterns that push a freight or reverse-logistics claim from "normal" to "warrants a closer look before payout or resubmission." Not a substitute for the company's legal review or insurer-led SIU process.

## Why this addendum exists

Two 2026 trends made a standalone reference worth keeping. First, generative-AI image tools are cheap and good enough that fraudulent damage photos are now routine on both inbound freight claims and B2C return-damage claims; multiple industry outlets have documented organized rings and individual actors submitting AI-rendered "damaged goods" images against shipments that arrived intact. Second, reverse-logistics volume is large enough — mid-teens percent of U.S. e-commerce returns are estimated to be fraudulent — that the claims desk is being drawn into a fraud-detection posture that used to sit only with loss prevention.

The goal of this reference is to give the claims-packaging skill a consistent way to flag indicators **without** making an accusation in the outbound document. Filings should still be factual; the indicators drive an *internal* decision on whether to add an evidence request before the carrier accepts liability or whether to route the file to the insurer's special-investigations workflow.

## Indicator families

### Image-evidence indicators

- Same damage shape, angle, or lighting appears across multiple unrelated claims from the same account, carrier lane, or remit-to address
- EXIF metadata missing, inconsistent with the claimed delivery window, or shows a device model the consignee has not historically used
- Damage visible in the photo is physically inconsistent with the packaging condition — broken product in an unscuffed box, water damage with no carton swell, impact damage with no corresponding pallet mark
- Watermarks, upscaler artifacts, or AI-generator signatures visible in the image (a known subset of generators still stamp output); reverse-image search hits from before the claimed delivery date
- Background context in the photo does not match the consignee address (wrong floor type on a warehouse claim, indoor shot on an outdoor-dock claim)

### Pattern indicators across the account

- Claim rate on the account is materially above the lane baseline without a commodity or packaging explanation
- Claims cluster around specific carriers or specific drivers in a way not supported by that carrier's broader performance
- Repeat "concealed damage" filings at the edge of the 15-day notification window
- Matching claim amounts across independent shipments — e.g., repeated filings at exactly the insured-value cap
- Same supporting vendor (inspection firm, repair shop, salvage buyer) appears across an implausible share of filings
- Remit-to bank routing on the claim does not match the remit-to on the original invoice

### Document indicators

- POD exception wording copied verbatim across unrelated deliveries
- Repair invoice font, layout, or total field pattern matches across invoices that claim to be from different shops
- Salvage credit is suspiciously low relative to the claimed damage — or salvage is declined without a documented reason
- Commercial invoice shows a declared value out of pattern with the customer's historical orders on the same SKU
- Temperature-recorder downloads cropped to hide the ambient-return segment or showing impossibly flat lines

### Behavioral indicators

- Claimant pushes for a fast cash settlement and declines an inspection the carrier is willing to fund
- Photos are supplied only after a written denial, not in the original filing
- Claimant refuses to hold salvage for pickup or insists on destroying evidence before the insurer inspects
- Escalation threats ("we will sue / we will post this on social") precede rather than follow a specific adjuster response

## How the claims-documentation-builder should use this file

The builder skill should:

1. On intake, score the claim on each indicator family as **None / Single / Cluster** (no red flags, one flag present, multiple flags from the same family or across families).
2. Add a single *internal-only* block to the output titled **Fraud-Indicator Review** listing which specific indicators tripped, with one line of evidence each. This block does not go to the carrier.
3. Route **Cluster** cases to the claims manager (or insurer SIU contact) before filing, and pause the automatic submission path if one exists.
4. Never insert accusatory language into the outbound cover letter. The filing itself stays neutral — the builder can add a sentence requesting specific additional evidence (a second set of dated photos with EXIF, a third-party inspection, a copy of the repair-shop business license) that is consistent with ordinary claims diligence.
5. Refer contested AI-image cases to the insurer rather than trying to prove authenticity unilaterally.

## How the carrier-fraud-screening-brief should use this file

The screening-brief skill should read the indicator families at three points:

1. **Onboarding** — If the counterparty's prior-claims history on external databases shows the pattern indicators, add a *Claims Pattern* note to the risk brief.
2. **Tender** — If a specific load is being tendered on a lane or to a counterparty that has previously tripped a Cluster claim, add a line to the tender-acceptance section recommending elevated pickup/delivery documentation (sealed photos at both ends).
3. **In-transit** — If an exception is called mid-transit by a counterparty whose claims history includes the behavioral indicators, flag the exception for claims-manager review at the moment the exception is logged, not at claim-filing time.

## Scope limits

- This reference is a workflow aid, not a liability framework. Carmack, COGSA, Montreal, and the insurance policy in force still govern. A flagged claim is not a denied claim.
- Individual indicators are weak signals. The decision point is whether a Cluster has formed, not whether a single flag is present.
- Nothing in this reference should be copied into customer-facing or carrier-facing correspondence. Everything here stays internal.
- The indicator list is meant to be pruned and extended over time by the claims team; treat it as a living document, not a spec.

## Cross-links

- `skills/admin/claims-documentation-builder.md` — primary consumer; adds the internal-only Fraud-Indicator Review block
- `skills/admin/carrier-fraud-screening-brief.md` — reads indicator families during onboarding, tender, and in-transit review
- `skills/admin/compliance-document-checker.md` — independent of this file; validates documents for a known-good shipment
- `knowledge-base/terminology/` — defines Carmack, concealed damage, SIU, salvage, subrogation, chameleon carrier, double brokering
