---
name: "BOL Generator"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~15 min/BOL"
version: 2.0
last_eval_score: null
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

- Load `config.yml` from the repo root for company name / address, default SCAC list, default freight terms, NMFC subscription status, and hazmat emergency-response contract vendor (Chemtrec account # if applicable)
- Reference `knowledge-base/terminology/` for correct terms (NMFC, freight class, density, stowability, handling, liability, SCAC, Pro #, VICS BOL)
- Reference `knowledge-base/regulations/` for hazmat marking requirements (49 CFR 172.200–172.205) and the shipper's-certification wording
- Use the company's communication tone from `config.yml` → `voice` for any cover-note or shipper-instructions text

**Process:**

1. **Validate the commodity and classify** — For each freight line, compute density (lb/ft³) from the dimensions and weight. Cross-check the stated freight class against the density band; flag mismatches. If an NMFC # is missing, propose the most likely match from the commodity description and note it as proposed, not confirmed. Never output a class without either an NMFC reference or a "CLASS TBD — confirm with carrier" flag
2. **Sanity-check weights and counts** — Confirm total piece count, total handling-unit count, and total weight add up across lines. Flag weight totals that look round-number-suspicious for the commodity (often a re-weigh trigger)
3. **Resolve hazmat** — If any line is hazmat, build the shipper's-declaration block exactly once per UN#: "UN#### , Proper Shipping Name, Class X, Packing Group Y, Quantity". Include the 24/7 emergency response number and the shipper's-certification statement. Confirm that a hazmat-trained signatory name is available; if not, flag the BOL as not-releasable until provided
4. **Assemble the BOL** — Populate the VICS-aligned section order: header (BOL #, date, carrier + SCAC, Pro #) → shipper → consignee → third-party-payor → special instructions → freight-charge terms (prepaid / collect / third-party) → commodity detail table → hazmat declaration (if any) → accessorial codes → declared value / COD → shipper's certification and signature lines → carrier pickup signature line
5. **Populate the commodity detail table** — Columns: Handling Unit Qty | HU Type | Package Qty | Package Type | Weight | HM (Y/N) | Commodity Description | NMFC # | Class. One row per SKU / class break. Subtotal weight and HU count at the bottom
6. **Draft accessorial codes** — Translate plain-English special instructions into standard accessorial codes (LIFT, INSDEL, RESDEL, NOTIFY, LTDACC, SORT, PROFRZ, etc.) where carrier-specific codes are known; otherwise list the accessorial in plain English and flag "carrier-coded at rating"
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

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
