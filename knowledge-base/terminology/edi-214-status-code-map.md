# EDI 214 Status-Code Map — Reference

Short reference consumed by `skills/operations/shipment-status-summarizer.md` and by any other skill that ingests carrier EDI 214 messages and needs to translate them into customer-facing milestone language consistently. The 214 (X12 Transportation Carrier Shipment Status Message) is the carrier's electronic shipment-status feed; the codes inside it travel between carrier systems and shipper / 3PL systems and are not by themselves a customer-facing vocabulary.

The goal of this map is to give every skill that reads a 214 a stable translation between the X12 status code and the milestone phrasing the customer-facing skills already use, and to flag the codes whose meaning is operationally ambiguous so the skills handle them with the right hedge rather than claim more than the code says.

## Where the codes live in the message

The shipment-status update sits inside the AT7 segment. The most consequential element is **AT701** — the *shipment-status code* — with the optional **AT702** *shipment-status reason code*, **AT703** *date / time qualifier*, and the date-time pair (**AT704 / AT705**) that establishes when the event happened and on what time-zone reference. Some carriers also populate an **AT706** *appointment-status code* on appointment-touching events.

The map below covers the AT701 codes that move the customer story. Reason codes (AT702) and appointment codes (AT706) are noted where they materially change the customer-facing translation.

## The codes that matter, mapped

### Pre-pickup and pickup events

- **AA — Pickup Attempted** — Carrier arrived but could not load. Customer-facing translation: "pickup attempted; load not yet on board." Reason-code (AT702) decides whether the customer needs to do anything (shipper not ready) or the carrier owns the recovery (equipment issue). Do not call this "delayed" until a recovery window has been confirmed
- **X3 — Arrived at Pickup Location** — Carrier on site, loading not yet started. Customer-facing translation: "carrier on site at origin." Useful in proactive notifications; not yet meaningful for ETA recalculation
- **AF — Carrier Departed Pickup Location with Shipment** — Pickup is complete; the shipment is officially in carrier custody on the move. Customer-facing translation: "picked up — in transit." This is the event that starts the in-transit clock for transit-time SLA reporting and for D&D-clock skills that key off pickup actuals (see `dd-invoice-audit-dispute-drafter.md`)
- **L1 — Loading** — Loading in progress. Some carriers send L1 only; some send X3 → L1 → AF. Treat L1 as an in-progress signal, not as a milestone in its own right unless the customer's portal vocabulary shows loading as a discrete status

### In-transit and lane events

- **P1 — Departed Terminal Location** — Inter-terminal departure. Customer-facing translation: depends on mode. Parcel and LTL portals usually surface this as "in transit"; on TL it is rarely worth surfacing to the customer. The summarizer skill should suppress P1 on TL unless paired with a meaningful location change
- **AG — Estimated Delivery** — Carrier-computed ETA. Customer-facing translation: "ETA: [date]." Pair with the ETA-confidence label `shipment-status-summarizer.md` already uses (Confirmed / Forecast / Tentative). Multiple AG events on the same shipment are normal and expected as the carrier refines; the summarizer should show only the latest with a "as of [time]" stamp
- **K1 — Arrived at Way Point** — Intermediate stop arrival, often a relay or interlining point. Surface only when the customer's view tracks intermediate stops; on most TL lanes, suppress
- **OA — Out for Delivery** — Driver has the load on the truck for the final leg of the day. Customer-facing translation: "out for delivery today." Highest-value notification on parcel and last-mile; less meaningful on LTL appointment freight

### Delivery and final-status events

- **X1 — Arrived at Delivery Location** — Carrier on site at destination, unloading not yet started. Customer-facing translation: "arrived for delivery." On appointment freight, pair with appointment-met / appointment-missed flag (AT706) when present
- **AJ — Tendered for Delivery** — On LTL and similar modes, the carrier has presented the freight at the consignee. On parcel, treat this as effectively interchangeable with attempt-event semantics
- **AV — Available for Delivery** — Carrier has the load and the consignee can take it; appointment may or may not be set. Customer-facing translation: "ready for delivery — appointment [set / pending]"
- **D1 — Completed Unloading at Delivery Location** — Unload complete. Often paired with a separate proof-of-delivery flow; do not mark a shipment delivered on D1 alone if the proof-of-delivery is the contractually controlling event
- **CB — Completed Shipment** — The carrier's "we are done" event. Customer-facing translation: "delivered." The summarizer skill should treat CB as the delivered milestone and suppress further status-code events that arrive after CB unless they reverse it (e.g., a return-pickup event for a refused delivery)
- **A3 — Shipment Returned to Shipper** — Refused or undeliverable, returned. Customer-facing translation: "returned to shipper." Trigger for `shipment-exception-handler.md` and for a customer-comms callback even if no other flag fires
- **A7 — Refused by Consignee** — Refusal at the door. Reason code (AT702) decides whether this is a damage refusal (route to `claims-documentation-builder.md`), an appointment refusal (route to `delivery-delay-apology-kit.md` and `dock-scheduling-detention-brief.md`), or a wrong-product refusal (route to commercial)

### Exception and delay events

- **AM — Shipment Damaged** — Carrier-flagged damage event. Trigger for `claims-documentation-builder.md`. Do not push a damage assertion to the customer until photos and the damage location have been confirmed
- **CA — Shipment Cancelled** — Cancellation event. Surface to the customer only after confirming the cancelling party — carrier-cancelled vs. shipper-cancelled vs. consignee-cancelled — because the customer-facing voice differs entirely
- **AH — Attempted Delivery** — Attempted but undelivered. Reason code (AT702) is essential. Re-attempt, redelivery-fee implications, and appointment-rescheduling all hang off this event

### Status / clarification events that look like milestones but are not

- **SD — Shipment Delayed** — Carrier-asserted delay. Because the AT702 reason code carries the operational meaning (weather, mechanical, traffic, customs hold, missing paperwork), `shipment-status-summarizer.md` should never surface SD without also surfacing the reason code's translation
- **OO — Paperwork Missing** / **OK — Paperwork Received** — Document-flow events. Useful internally; usually not customer-facing unless the missing paperwork is the consignee's responsibility
- **CP — Completed Pickups** — Daily roll-up event from some carriers; not a per-shipment milestone in the way it reads at first
- **NS — Normal Status, No Problems Bumping Schedule** — A "we are on track" assertion. The summarizer should treat NS as an explicit no-change confirmation rather than as an event worth notifying on

## How the summarizer skill should consume this map

The summarizer skill should:

1. Take the carrier 214 message in, extract the AT7 segment(s), and translate AT701 (and AT702 where relevant) using this map
2. Suppress code events that this map flags as not customer-facing on the relevant mode (P1 and L1 on TL; CP on any mode), and roll forward only the most recent of any code that fires repeatedly (AG)
3. Pair every customer-surfaced event with the timestamp from AT704 / AT705 normalized to the customer's time zone of record, and with the location string the carrier provided
4. Use the existing ETA-confidence vocabulary (Confirmed / Forecast / Tentative) on AG events; do not invent new confidence labels
5. Route exception events (AM, A7, AH with mechanical or refusal reason codes, SD with severe reason codes) to the appropriate downstream skill rather than absorbing them silently

## Scope limits

This map is a working translation reference, not the X12 specification. It covers the AT701 codes the operator's skills consume in normal working conditions. It does not enumerate every published status code, every reason code (AT702), every date-time qualifier (AT703), every appointment-status code (AT706), or the segment-and-element structure of a 214 message in detail. The X12 specification, the carrier's implementation guide, and the company's EDI integration documentation are the source of truth for any code or behavior not covered here.

## Maintainer notes

- Carrier 214 implementations vary materially. The same status code can have slightly different operational meaning between carriers (an L1 sent by a parcel carrier and an L1 sent by a TL carrier are not the same operator event). Where a carrier consistently misuses a code, capture the carrier-specific override in `config.yml` rather than overloading this map
- The map is intentionally translation-focused, not enumeration-focused. Adding rare codes that the company has not actually seen on its lanes adds maintenance burden without benefit; add codes when the team starts seeing them in real traffic
- When `shipment-status-summarizer.md` is updated to support a new mode (a new ocean code set, an air-cargo IATA code set), spin out a separate map (`iata-air-cargo-status-codes.md`, `inttra-ocean-event-codes.md`) rather than mixing modes in this file. The 214 is specifically the X12 over-the-road / multimodal LTL-and-TL feed; ocean and air have their own equivalents
