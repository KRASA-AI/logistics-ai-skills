---
name: "BOL Generator"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~15 min/BOL"
version: 2.2
last_eval_score: 8.8
---

# 📄 BOL Generator

## Purpose

Turn raw shipment details into a VICS-aligned Bill of Lading with correctly classified freight (NMFC + class), properly sequenced shipper / consignee / carrier / third-party-payor blocks, accurate handling-unit and weight math, and any required hazmat declarations — produced in a format the shipping clerk can sign, the driver can take, and the carrier's imaging system will read cleanly on the first pass.

## When to Use

Use this skill any time a BOL needs to be generated or reconstructed: originating a new outbound LTL, TL, or parcel-over-LTL move; reissuing a corrected BOL after a classification or weight change; generating a bill of lading for a rework load out of the warehouse; or converting a customer's SOP / pick ticket into a carrier-ready document. It also covers the hazmat add-on (shipper's declaration language and emergency-response info) when the shipment contains regulated material.

This skill produces a signature-ready document, but the shipper-of-record signature and the driver's signature-upon-pickup remain human actions. If the NMFC code or freight class is not obviously correct from the commodity description, the output flags this rather than guessing.

## Required Input

Provide the following:

1. **Shipment header** — Pickup date, ready time, BOL number (if assigned) or the numbering scheme to use, PO # or customer reference, carrier name + SCAC, carrier Pro # (if tendered), service level (standard LTL, guaranteed, volume, TL)
2. **Shipper block** — Legal name, address, city/state/ZIP, contact name, phone, dock hours, shipper-of-record flag (yes/no)
3. **Consignee block** — Legal name, address, city/state/ZIP, contact, phone, receiving hours, appointment # if required
4. **Third-party payor (if bill-to ≠ shipper or consignee)** — Name, address, billing contact, account # with carrier
5. **Freight detail per line** — Handling-unit count (pallets / crates / bundles), handling-unit type, piece count inside each unit, dimensions L×W×H, weight, commodity description (plain English + NMFC # + freight class if known), NMFC sub if applicable, packaging type, stackable (yes/no), condition (new / used / rebuilt)
6. **Hazmat (if any regulated material)** — UN# / NA#, proper shipping name, hazard class, packing group, quantity per package, emergency response phone (24/7), emergency response guide (ERG) # if relevant, hazmat contact / shipper's certification signatory
7. **Special instructions** — Liftgate (pickup / delivery), inside delivery, residential, notify before delivery, limited-access site, sort-and-segregate, protect-from-freeze, temperature range, COD amount, declared value, insurance add-on
8. **Freight terms** — Prepaid / collect / third-party; freight charges marked prepaid & collect where applicable

## Instructions

You are a freight-documentation specialist's AI assistant. Your job is to produce a clean, release-ready bill of lading, to catch the classification and weight mistakes that cause reweighs and rebills, and to flag any input that would make the BOL non-compliant if released as drafted.

**Before you start:**

- Load `config.yml` from the repo root and pull the BOL-defaults profile so the document is pre-populated from the company's standing setup rather than re-entered each time:
  - `company` block → shipper-of-record legal name, address, SCAC (if the company is also a carrier), default dock hours, and the shipper-certification signatory roster (`bol.signatories` — the hazmat-trained and non-hazmat names authorized to sign, so the certification block fills with a real name instead of a blank)
  - `bol.numbering_scheme` → the company's BOL-number format (e.g., `BOL-{YYYY}-{MMDD}-{customer3}-{seq}`) so the BOL # is generated to the house standard, not invented
  - `bol.default_carriers_by_lane` and `bol.scac_list` → the carrier + SCAC the company tenders by lane/service, so the carrier block pre-fills when the user names a lane instead of a carrier
  - `bol.default_freight_terms` → prepaid / collect / third-party default, plus the standing third-party-payor block (name, address, account #) for collect/3rd-party customers
  - `bol.standing_accessorials` → per-customer or per-consignee standing accessorial defaults (e.g., "Midwest Retail DC always requires liftgate + 2-hr notify"), so recurring lanes pre-populate their accessorial codes
  - `bol.nmfc_subscription` → subscription status + tier (drives whether NMFC sub-classes can be confirmed vs. only proposed) and the company's saved NMFC map for its top recurring SKUs (so repeat commodities classify from the company's own confirmed history, not a fresh guess)
  - `bol.declared_value_rule` → the company's default declared-value / released-value posture and any per-customer insurance add-on rule
  - `bol.hazmat_vendor` → hazmat emergency-response contract vendor and account # (Chemtrec or equivalent) so the 24/7 ERG number fills automatically on hazmat moves
  - Any field the user supplies on the specific shipment overrides the config default; note in the gap list when a config default was used so the clerk can confirm it still applies
- Reference `knowledge-base/terminology/` for correct terms (NMFC, freight class, density, stowability, handling, liability, SCAC, Pro #, VICS BOL)
- Reference `knowledge-base/regulations/` for hazmat marking requirements (49 CFR 172.200–172.205) and the shipper's-certification wording
- Use the company's communication tone from `config.yml` → `voice` for any cover-note or shipper-instructions text

**Process:**

1. **Validate the commodity and classify** — For each freight line, compute density (lb/ft³) from the dimensions and weight. Cross-check the stated freight class against the density band; flag mismatches. Before guessing, check the commodity against the company's saved NMFC map in `config.yml` (`bol.nmfc_subscription.saved_map`) — for recurring SKUs the company ships, this yields a class the company has already confirmed, which can be marked *confirmed-from-company-history* rather than *proposed*. If an NMFC # is missing and the commodity is not in the saved map, propose the most likely match from the commodity description and note it as proposed, not confirmed. Never output a class without either an NMFC reference or a "CLASS TBD — confirm with carrier" flag
2. **Sanity-check weights and counts** — Confirm total piece count, total handling-unit count, and total weight add up across lines. Flag weight totals that look round-number-suspicious for the commodity (often a re-weigh trigger)
3. **Resolve hazmat** — If any line is hazmat, build the shipper's-declaration block exactly once per UN#: "UN#### , Proper Shipping Name, Class X, Packing Group Y, Quantity". Include the 24/7 emergency response number and the shipper's-certification statement. Confirm that a hazmat-trained signatory name is available; if not, flag the BOL as not-releasable until provided
4. **Assemble the BOL** — Populate the VICS-aligned section order: header (BOL #, date, carrier + SCAC, Pro #) → shipper → consignee → third-party-payor → special instructions → freight-charge terms (prepaid / collect / third-party) → commodity detail table → hazmat declaration (if any) → accessorial codes → declared value / COD → shipper's certification and signature lines → carrier pickup signature line
5. **Populate the commodity detail table** — Columns: Handling Unit Qty | HU Type | Package Qty | Package Type | Weight | HM (Y/N) | Commodity Description | NMFC # | Class. One row per SKU / class break. Subtotal weight and HU count at the bottom
6. **Draft accessorial codes** — Translate plain-English special instructions into standard accessorial codes (LIFT, INSDEL, RESDEL, NOTIFY, LTDACC, SORT, PROFRZ, etc.) where carrier-specific codes are known; otherwise list the accessorial in plain English and flag "carrier-coded at rating". First apply any standing accessorial defaults from `config.yml` (`bol.standing_accessorials`) keyed to this consignee or lane — e.g., a consignee flagged as no-dock-high pre-populates DLIFT + NOTIFY — so recurring sites carry their known requirements without re-entry. Mark config-sourced accessorials in the gap list as "standing default — confirm still applies"
7. **Prepare the release checklist** — Before handing the BOL to the driver: BOL # recorded, Pro # space is present (blank or filled), hazmat block matches the freight (if any), third-party-payor account # filled (if applicable), all weights match the pallet scale, shipper-of-record signature line present, driver signature line present, two copies at minimum (shipper retains, driver carries; original + image to carrier TMS)
8. **Write the one-line handoff note** — A short note to the warehouse / shipping clerk: "BOL 874621 ready for XPO pickup 04/14 14:00–16:00. 6 pallets, 2,240 lb, 2 class breaks (class 70 + class 125). No hazmat. Liftgate delivery required."

**Output requirements:**

- **Header block** — Company name, address, phone, SCAC; BOL #; date; carrier; Pro # field; freight terms
- **Shipper / Consignee / Third-Party-Payor blocks** — Named, addressed, contacted
- **Commodity detail table** — HU Qty | HU Type | Pkg Qty | Pkg Type | Weight | HM | Description | NMFC # | Class, with row totals
- **Hazmat declaration block** — Exactly as 49 CFR 172.202 requires, with emergency response info, OR explicitly omitted with "No hazardous materials on this shipment"
- **Accessorial code list** — Carrier-standard codes where known
- **Special instructions** — Free-text block
- **Declared value / COD** — Populated or explicitly "N/A"
- **Shipper certification + signature lines** — Signatory name (typed), signature line, date, time
- **Carrier pickup signature lines** — Driver name, signature line, date, time, piece count at pickup
- **Gap list** — Any input that was missing, guessed, or proposed-not-confirmed (classifications, NMFC #s, hazmat signatory, Pro #)
- **Release checklist** — The pre-handoff 8-point check
- Do not output the BOL as "final" if any gap is open — label it "DRAFT — resolve gaps before release"
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — 2-class LTL move, dry van, no hazmat, liftgate delivery required).

**Header block**

| Field | Value |
|---|---|
| BOL # | BOL-2026-0511-ACM-0074 |
| Date | 2026-05-11 |
| Carrier | Estes Express Lines |
| SCAC | EXLA |
| Pro # | (blank — to be assigned by carrier at pickup) |
| Freight terms | Prepaid |
| Service level | Standard LTL |
| PO / Customer ref | PO-88421 |
| Pickup date / ready time | 2026-05-12 · 08:00 CT |

**Shipper block**

| Field | Value |
|---|---|
| Legal name | Acme Distribution LLC |
| Address | 1800 Industrial Pkwy, Cincinnati, OH 45217 |
| Contact | D. Hoffman — dock supervisor |
| Phone | 513-555-0192 |
| Dock hours | 07:00–15:00 CT Mon–Fri |
| Shipper of record | Yes |

**Consignee block**

| Field | Value |
|---|---|
| Legal name | Midwest Retail DC, Inc. |
| Address | 4400 Logistics Blvd, Columbus, OH 43228 |
| Contact | Receiving dept. |
| Phone | 614-555-0374 |
| Receiving hours | 07:00–16:00 ET Mon–Fri |
| Appointment required | No |

**Third-party payor**

N/A — freight terms prepaid, bill-to is shipper of record.

**Special instructions**

Liftgate delivery required at consignee (no dock-high door). Notify consignee 2 hr before delivery: 614-555-0374.

**Freight terms**

Freight charges: PREPAID. Carrier is authorized to deliver without collect charges.

**Commodity detail table**

| HU Qty | HU Type | Pkg Qty | Pkg Type | Weight (lb) | HM | Commodity Description | NMFC # | Class |
|---|---|---|---|---|---|---|---|---|
| 4 | Pallets | 48 | Cartons | 1,920 | N | Automotive brake pads, new — plastic packaged | 36840 sub 1 | 55 |
| 2 | Pallets | 16 | Cartons | 480 | N | Corrugated packaging material, flat sheets | 56050 sub 2 | 100 |
| **6** | | **64** | | **2,400** | | | | |

*Density check — Line 1: 48 cartons × avg 40 lb = 1,920 lb; pallet dims 48×40×54 in → 60 lb/ft³ → class 55 confirmed. Line 2: 16 cartons × avg 30 lb = 480 lb; pallet dims 48×40×36 in → 10 lb/ft³ → class 100 confirmed. No class mismatch.*

**Hazmat declaration**

No hazardous materials on this shipment.

**Accessorial codes**

| Code | Description | Trigger |
|---|---|---|
| DLIFT | Liftgate — delivery | Consignee has no dock-high door (per special instructions) |
| NOTIFY | Notify before delivery | 2-hr advance call required |

*No pickup liftgate required — shipper has dock-high doors.*

**Declared value / COD**

Declared value: N/A (released value per carrier tariff applies). COD: N/A.

**Shipper certification and signature lines**

> This is to certify that the above-named materials are properly classified, described, packaged, marked, and labeled, and are in proper condition for transportation according to the applicable regulations of the DOT.

Signatory (shipper of record): D. Hoffman, Acme Distribution LLC
Signature: _________________________ Date: ____________ Time: _______

**Carrier pickup signature lines**

Driver name (printed): _________________________
Driver signature: _________________________ Date: ____________ Time: _______
Piece count confirmed at pickup: _______ pallets / _______ cartons

**Gap list**

| Gap | Type | Action required |
|---|---|---|
| Carrier Pro # | Informational only | Carrier assigns at pickup; leave field blank on this copy, record on shipper's copy when driver annotates |
| NMFC #36840 sub 1 (brake pads) | Proposed — confirm | NMFC subscription confirms sub 1 applies to non-asbestos brake pads in retail packaging; carrier may reclassify if shipment is repackaged as bulk. *Proposed, not confirmed.* |
| NMFC #56050 sub 2 (corrugated sheets) | Proposed — confirm | Sub 2 applies to flat corrugated sheets on pallets; verify with carrier if sheets are cut-to-size or pre-printed. *Proposed, not confirmed.* |

**Status: DRAFT — resolve NMFC sub confirmations before treating as final release copy.**

**Release checklist (pre-handoff 8-point)**

| # | Check | Status |
|---|---|---|
| 1 | BOL # recorded | ✅ BOL-2026-0511-ACM-0074 |
| 2 | Pro # space present (blank for carrier assignment) | ✅ |
| 3 | Hazmat block matches freight (no hazmat on this shipment) | ✅ Explicitly stated |
| 4 | Third-party-payor account # (N/A — prepaid) | ✅ |
| 5 | All weights match pallet scale | ⬜ Verify before tender — scale confirmation pending |
| 6 | Shipper-of-record signature line present | ✅ |
| 7 | Driver signature line present | ✅ |
| 8 | Two copies prepared (shipper retains, driver carries) | ⬜ Print two copies before pickup window |

**One-line handoff note to warehouse / shipping clerk**

> BOL-2026-0511-ACM-0074 ready for EXLA pickup 05/12 08:00–10:00 CT. 6 pallets, 2,400 lb, 2 class breaks (class 55 brake pads + class 100 corrugated). No hazmat. Liftgate delivery required at Columbus. Confirm pallet weights on dock scale before driver signs.

*In this example the BOL number was generated to the house `bol.numbering_scheme` (`BOL-{YYYY}-{MMDD}-{customer3}-{seq}` → `BOL-2026-0511-ACM-0074`), the shipper block and signatory (D. Hoffman) pulled from `config.yml` rather than re-keyed, the DLIFT + NOTIFY accessorials applied from the Columbus consignee's `bol.standing_accessorials` entry, and both class breaks confirmed from the company's saved NMFC map — each config-sourced field is echoed in the gap list as "confirm still applies."*

## Configuration Reference

- `config.yml` — `company.name` / `company.address` / `company.scac` (shipper-of-record header), `bol.signatories` (hazmat-trained + non-hazmat certification signatory roster), `bol.numbering_scheme` (house BOL-number format), `bol.scac_list` and `bol.default_carriers_by_lane` (carrier + SCAC pre-fill by lane/service), `bol.default_freight_terms` (prepaid / collect / third-party default) and the standing third-party-payor block, `bol.standing_accessorials` (per-consignee / per-lane standing accessorial defaults), `bol.nmfc_subscription` (subscription status + tier + `saved_map` of confirmed classes for recurring SKUs), `bol.declared_value_rule` (default declared/released-value posture + per-customer insurance add-on), `bol.hazmat_vendor` (Chemtrec-or-equivalent account # for the 24/7 ERG number), `voice` (tone for cover-note / shipper-instructions text). Shipment-specific input always overrides a config default; every config-sourced field is surfaced in the gap list as "confirm still applies."
- Knowledge base — `knowledge-base/terminology/` (NMFC, freight class, density, stowability, SCAC, Pro #, VICS BOL definitions), `knowledge-base/regulations/` (49 CFR 172.200–172.205 hazmat marking + shipper's-certification wording)
- Sibling skills — `skills/admin/compliance-document-checker.md` (pre-release hazmat-BOL compliance review of the document this skill produces), `skills/admin/claims-documentation-builder.md` (uses the BOL as the controlling tender-condition exhibit when a loss claim follows)
