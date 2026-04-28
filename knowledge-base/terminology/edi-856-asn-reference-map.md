# EDI 856 ASN Reference Map — Reference

Short reference consumed by `skills/operations/shipment-status-summarizer.md` (pre-arrival forecast layer), `skills/admin/claims-documentation-builder.md` (count-of-record on the receiving side), and `skills/admin/compliance-document-checker.md` (vendor-compliance retailer-mandate checks). Sibling to `knowledge-base/terminology/edi-214-status-code-map.md`. The 214 carries the carrier's status feed across the in-transit window; the 856 (X12 Ship Notice / Manifest, conventionally called the *Advance Ship Notice* or ASN) carries the supplier's advance description of the shipment to the consignee — what is on the truck, how it is packed, and when it will arrive — *before* the truck physically gets there.

The goal of this map is to give every skill that reads an 856 a stable understanding of the message's hierarchical shape, the elements that drive the customer-facing "what's arriving and when" view, and the vendor-compliance traps that drive retailer chargebacks.

## What the 856 is and why it matters

The 856 is the supplier-or-carrier-to-consignee announcement message. It is the basis for cross-docking, for receiving-by-exception (the consignee's WMS expects pallet *X* with case *Y* containing units *Z*; receiving validates against the ASN and flags only what does not match), for pre-receipt putaway planning, and for downstream automation. Major retailers (the Walmart / Target / Costco / Lowe's / Home Depot / Amazon vendor-compliance programs) require the ASN to land before the truck does, and assess chargebacks when it doesn't or when its content is wrong. For a 3PL or supplier shipping into vendor-compliance retail, the ASN's accuracy is a margin line.

Operationally, the 856 also feeds three other workflows the repo's existing skills touch:

- **Pre-arrival customer view.** The *what's arriving today / tomorrow* portal view runs largely off ASN data, supplemented by the in-transit 214 and the carrier's tracking feed
- **Receiving-side count of record.** When a shortage or damage claim is later filed, the ASN is the documented "what was promised" against which the receiving count is compared
- **Compliance-document validation.** Where a retailer's compliance program mandates an ASN, the absence or lateness of an ASN is itself a documented breach, with chargeback consequence

## The hierarchical shape

The 856 is hierarchical. It uses the X12 **HL** (hierarchical-loop) segment to nest contents inside containers, and containers inside shipments. The most common usage pattern is the **SOPI** model — Shipment / Order / Pack / Item — though some implementations use SOI (no pack level) or SP (shipment / pack only, with no order separation), and warehouse-to-warehouse transfer-style ASNs sometimes use SOTPI (with a Tare-and-Pack subdivision for mixed-pallet shipments).

Each HL segment carries:

- **HL01** — Hierarchical ID Number (a sequence within the message)
- **HL02** — Hierarchical Parent ID (which prior HL this one nests under)
- **HL03** — Hierarchical Level Code (S = Shipment, O = Order, T = Tare, P = Pack, I = Item, plus several others)

Reading an 856 is reading the HL tree. A typical case-pack ASN: one HL*S at the top; one or more HL*O nested under the shipment (one per purchase order on the truck); one or more HL*P nested under each order (one per carton or per pallet); one or more HL*I nested under each pack (the items inside that carton or pallet).

The skills that consume the 856 should treat the HL tree as the primary structure, not the flat list of segments.

## Segments and elements that move the customer story

### Header and shipment identity

- **ST / BSN** — Transaction-set header and the *Beginning Segment for Ship Notice*. The BSN carries the operator-relevant identifiers
- **BSN02 — Shipment Identification** — The unique ASN number. Reusing a prior ASN ID without a corrective purpose code is the most common rejected-ASN cause on retail vendor-compliance programs
- **BSN03 / BSN04 — Date and Time** — When the ASN was generated. Used by retailers to enforce the *ASN before truck* timing rule
- **BSN05 — Hierarchical Structure Code** — Tells the consignee which HL pattern this ASN follows (0001 = SOPI, 0002 = SOI, 0004 = SP, etc.). Reading this code first tells the parser what tree shape to expect
- **BSN06 — Transaction Set Purpose Code** — The most operationally consequential field. *00* = Original (this is a new ASN), *05* = Replace (this ASN supersedes a prior one with the same BSN02), *07* = Duplicate (this is intentionally a re-send of a prior ASN), *01* = Cancellation (this ASN cancels a prior one). The summarizer skill should treat 00 and 05 as customer-facing milestones, 07 as a no-op, and 01 with care: the cancellation of a prior ASN is a customer-facing event, but the customer voice is "shipment notice withdrawn," not "shipment cancelled" (the underlying load may still be moving — only the *notice* is cancelled in the EDI flow)

### Carrier and equipment details

- **TD1** — Carrier details: package code, weight, freight class, lading description. Useful for the warehouse-side dock-prep view
- **TD3** — Equipment details: equipment type, equipment number, seal number. The seal number is what gets reconciled at gate against the BOL on physical pickup; mismatches route to `pickup-identity-verification-brief.md`
- **TD5** — Carrier identification: SCAC (TD502 — *Identification Code*, qualifier 02 = SCAC), routing code, transportation method. SCAC is the field every downstream skill reads first to know which carrier's 214 feed and which carrier's tracking page applies

### Reference numbers

- **REF*BM — Bill of Lading number** (the master BOL the carrier issued)
- **REF*CN — Carrier's reference number** (PRO number on LTL; carrier reference on TL)
- **REF*PK — Packing List number** (when packing-list is filed separately from the ASN)
- **REF*VR — Vendor reference** (the supplier's internal shipment ID — useful for cross-system reconciliation)
- **REF*ASN — ASN reference** (some implementations carry an additional ASN-tracking reference distinct from BSN02)

These reference numbers are the keys that link the 856 to the 214 (in-transit), to the 944 (warehouse-receipt advice), to the 990 (response-to-load-tender), and to the 810 (invoice). The summarizer and claims-builder skills should preserve all reference numbers from the ASN even when they look redundant — they are the linkage to downstream feeds.

### Date / time on the shipment

- **DTM*011 — Shipped date** (the actual or planned ship date)
- **DTM*017 — Estimated delivery date**
- **DTM*067 — Current schedule delivery date** (the carrier's best estimate)
- **DTM*068 — Current schedule pickup date**

The summarizer skill should pair the 856's date set with the 214's `AG` event (carrier-computed ETA) and prefer the most recent / most authoritative source for the customer view, with the existing Confirmed / Forecast / Tentative ETA-confidence vocabulary applied. Where the 856 says delivery on date *X* and the 214 says delivery on date *Y*, the discrepancy is a customer-facing exception unless the gap is small enough to be inside the carrier's normal AG-refinement noise.

### Parties

- **N1*SF — Ship From** (origin)
- **N1*ST — Ship To** (destination)
- **N1*BT — Bill To** (consignee billing entity, where different from ship-to)
- **N1*MA — Manufacturer / Supplier**
- **N1*CN — Consignee** (where different from ship-to)

Each N1 loop carries an identification code (DUNS, GLN, retailer-assigned vendor ID) in N104. Mismatches between the ASN's parties and the corresponding 850 (purchase order) are a routine vendor-compliance reject path.

### Pack-level identification (the SSCC layer)

- **MAN*GM*<SSCC18>** — The 18-digit Serial Shipping Container Code that uniquely identifies a logistics unit (typically a pallet or a master case). The MAN segment on each HL*P is the link between the ASN's described pack and the GS1-128 / SSCC-18 barcode physically on the pallet. When the receiver's WMS scans the SSCC at the dock, the WMS finds the matching HL*P in the ASN and inherits the entire pack-and-item structure underneath it. A missing or mismatched MAN segment is the dominant ASN-receiving-failure mode on retail vendor-compliance programs

### Item-level identification

- **LIN** — Item identification: UPC (qualifier UP), GTIN (qualifier UK or EN), retailer-assigned item number (qualifier IN or VN), buyer's part number (BP), supplier's part number (SK)
- **SN1 — Quantity shipped at the line level**
- **PRO** — Production date / lot / serial information where required (lot-tracked categories, food, pharma, regulated commodities)
- **REF*LO — Lot number**, **REF*XB — Serial number**, **DTM*036 — Expiration date**, **DTM*510 — Manufacture date**

For lot-tracked or expiration-dated commodities (pharma cold chain, food, regulated), the LIN/PRO/DTM elements are the basis for the receiving-side traceability obligation. The claims-builder and compliance-checker skills should treat missing lot or expiration on a lot-tracked commodity as a Pass / Flag / Fail issue, not as a no-op.

### Trailer

- **CTT — Transaction Totals** — Hierarchical-line count and (optionally) the hash total for the line items. The hash total is the cheapest cross-check on whether the ASN was transmitted intact
- **SE — Transaction Set Trailer**

## Compliance traps

### Late ASN

The dominant retail vendor-compliance trap. The 856 must arrive before the physical shipment. Different programs measure differently — some require ASN N hours before truck arrival at the DC, some require ASN before midnight on the ship date, some require ASN at the moment the BOL is issued. The compliance-checker skill should encode the active program's rule and flag late-ASN risk as soon as the BOL is generated against an ASN that has not been transmitted.

### Inaccurate ASN

The second-largest retail vendor-compliance trap. The classic patterns:

- **Carton-count mismatch** — ASN says 24 cartons; truck arrives with 23. The compliance program records this as an ASN-accuracy failure regardless of which side caused the discrepancy
- **SSCC mismatch** — The pallet's barcode is not the SSCC the ASN announced. The receiver's WMS cannot match, the pallet falls out to manual receiving, and the chargeback follows
- **Wrong pack quantity** — The ASN says 12-each on the carton; the carton actually contains 6-each (or 12-each of the wrong pack). Receiving sees the variance and the chargeback fires
- **Wrong UPC / GTIN** — The ASN's LIN UP code does not match the physical product. Disposition is usually receive-and-flag rather than receive-and-deny, but the chargeback applies

### Missing required fields

Each retailer's vendor-compliance program publishes a required-field set. The compliance-checker skill should validate the ASN against the active program's required-field set before transmission rather than relying on the receiver's reject feedback as the validation loop. Receiver feedback comes back as the 824 (Application Advice) message; treating the 824 as the validation gate is a slow loop with chargeback exposure on every reject.

### Duplicate ASN ID without purpose code 7

If the ASN is re-sent for any reason (ops re-keyed, the EDI VAN dropped a copy, a system retry fired), the resend must carry BSN06 = 07 (Duplicate). A re-send with BSN06 = 00 (Original) will be rejected as a colliding ASN ID, and the receiver's compliance program will record the rejection as an ASN failure even when the underlying shipment is fine.

### Multiple ASNs against one PO with overlap

When a single PO ships in multiple loads (a backorder pattern), each load gets its own ASN, and the BSN02 IDs must be distinct. The HL*O reference back to the same PO is fine; the BSN02 collision is not. The compliance-checker skill should flag this case at ASN generation time when the PO already has an open ASN against it.

### Cancellation handling

A cancelled ASN (BSN06 = 01) does not by itself cancel the underlying load — only the EDI notice is cancelled. The customer-facing voice on a cancelled ASN should be calibrated accordingly: the operator's *notice* about the shipment was withdrawn, the *load* may or may not still be moving. The summarizer skill should treat the cancellation as a "watch this shipment" event rather than a "shipment cancelled" event, and route to the load-status check on the actual shipment record before any customer message goes out.

## How the consuming skills should use this map

### `shipment-status-summarizer.md`

1. Treat the 856 as the *pre-arrival forecast* layer: when a load has an ASN but no 214, the ASN's date set drives the customer-facing "expected" view. When a load has both, the 214's `AG` event takes precedence on ETA, with the ASN as the cross-check
2. Surface the 856 as a customer milestone when BSN06 is 00 (Original) or 05 (Replace). Suppress 07 (Duplicate). Treat 01 (Cancellation) as a *watch* event rather than a delivered or cancelled event
3. On a Replace ASN (05), use the new content; suppress the prior ASN's milestones from the customer timeline rather than showing two pre-arrivals
4. Use the SCAC from TD5 to decide which carrier's 214 feed and which carrier's tracking page links to surface alongside the ASN data
5. On lot-tracked or expiration-dated commodities, surface the lot and expiration to the customer when the customer's portal supports it; otherwise keep them in the internal record for the receiving and claims feeds

### `claims-documentation-builder.md`

1. On a shortage or damage claim, the ASN is the documented count-of-record for *what was promised*. Compare the ASN's HL*P / HL*I count and quantity against the receiving side's 944 quantity (or the consignee's manual receiving count if no 944 was filed). The variance is the basis for the claimed-quantity field
2. On a SSCC-mismatch case, the ASN's MAN segment is the evidence that the pallet the consignee scanned was not the pallet the supplier announced. Capture the SSCC in the claim's evidence index
3. On a lot-tracked commodity claim (recall, expiration, contamination), the ASN's PRO / DTM / REF segments are the traceability evidence the claim builder must preserve for the insurer and (where applicable) the regulator
4. The ASN's reference numbers (REF*BM, REF*CN, REF*PK, REF*VR) are the linkage to the BOL, the carrier feed, and the supplier's internal records. Preserve all of them in the claim's exhibit list even when they look redundant

### `compliance-document-checker.md`

1. For shipments into vendor-compliance retail, validate the ASN at the moment of generation against the active retailer's required-field set, late-window rule, and duplicate-ID rule. Fail the validation rather than letting the 824 reject feedback be the validation loop
2. For lot-tracked or regulated commodities, validate the LIN / PRO / DTM presence and content against the company's classification rules. Missing or malformed lot or expiration data is a Flag-or-Fail event, not a Pass
3. Where the company's ASN generation pulls from a TMS or ERP that does not natively support all required fields, flag the gap as a maintenance item with the controlling program named (this is the kind of structural finding that the compliance-checker skill is designed to surface; see also `dock-scheduling-detention-brief.md` on the operational pattern of recurring vendor-compliance failures)

## Relationship to the 214

The 214 and the 856 are *complementary*, not redundant. The 214 carries the carrier-side *what is the load doing right now* feed across the in-transit window; the 856 carries the supplier-side *what is on the truck and when will it arrive* announcement that goes to the consignee directly. A load can have an 856 without a 214 (the supplier filed an ASN; the carrier is not on the company's 214 feed), a 214 without an 856 (the operator is moving a non-vendor-compliance load), or both. The summarizer skill stitches the two together when both are present and falls back gracefully when only one is.

The two reference maps share a deliberate pattern: each one tells the consuming skills what the message is, what the elements that move the customer story are, where the ambiguity lives, and how the skill should consume the message. Rate tables, code lists, and segment-and-element specifications are deliberately not embedded — those are pulled from the X12 specification and the trading partner's implementation guide on every run.

## Scope limits

This map is a working translation reference, not the X12 specification. It covers the segments and elements the operator's skills consume in normal working conditions. It does not enumerate every BSN purpose code, every HL hierarchical level code, every TD package code, every reference qualifier, every date qualifier, every entity-identifier qualifier, or the segment-and-element rules the X12 specification defines. The X12 specification (V004010, V005010, or whichever release the trading partner uses), the trading partner's ASN implementation guide, and the company's EDI integration documentation are the source of truth for any segment or behavior not covered here.

## Maintainer notes

- 856 implementations vary materially by retailer and by industry. The Walmart 856 is not the Target 856 is not the pharmaceutical-DSCSA 856 is not the warehouse-transfer 856. Where a trading partner's implementation differs from this map's general pattern, capture the partner-specific override in `config.yml` rather than overloading this map
- The map is intentionally translation-and-trap-focused, not enumeration-focused. Adding rare segments the company has not seen on its lanes adds maintenance burden without benefit; add segments when the team starts seeing them in real traffic
- Sibling-file backlog: `iata-air-cargo-status-codes.md` (the air-cargo-mode equivalent of the 214); `inttra-ocean-event-codes.md` (the ocean-mode equivalent of the 214); `edi-810-invoice-reference-map.md` (the invoice-side parallel of this file, when the company's claims and disputes work needs the cross-link to the 810 segment structure). Build any of these when an operator workflow needs them
- Where the 856 and the 214 disagree on a customer-facing field — the ASN's DTM*017 says delivery on Tuesday; the 214's `AG` says Wednesday — the summarizer skill should hedge rather than pick. The customer voice is "Tuesday per the supplier; Wednesday per the carrier; we are reconciling." That posture is consistent with the existing Confirmed / Forecast / Tentative ETA-confidence vocabulary
