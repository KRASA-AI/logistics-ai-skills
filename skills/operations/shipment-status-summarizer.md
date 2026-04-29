---
name: "Shipment Status Summarizer"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~10 min/update"
version: 2.1
last_eval_score: 8.7
---

# 📦 Shipment Status Summarizer

## Purpose

Take raw tracking events from one or more modes (parcel, LTL, TL, ocean, air, drayage, last-mile) and produce a concise, audience-appropriate status update that normalizes the milestone language, highlights exceptions with their business impact, states a confident or caveated ETA, and surfaces the one or two things that actually need a human decision — so the CSR, account manager, or executive reader gets signal, not a log dump.

## When to Use

Use this skill for daily "where's my freight" questions, for proactive customer updates on monitored shipments, for executive-level multi-shipment rollups ("how's our inbound this week"), for account-review prep (on-time %, exception count, claim exposure), and any time a tracking report from a carrier, portal, or visibility platform (Project44, FourKites, MacroPoint, SONAR, Flexport) needs to be converted into something a non-logistics reader can use.

It produces a report, not a correction. If an exception requires a dispatcher or carrier action (appointment reset, recovery plan, re-icing), the skill flags the action but does not execute it — route that to the exception handler.

## Required Input

Provide the following:

1. **Shipment identifier(s)** — Load # / BOL # / Pro # / container # / MAWB / HAWB / tracking # (list all relevant IDs for multi-leg moves)
2. **Mode(s) and legs** — Parcel, LTL, TL, ocean (FCL/LCL), air, drayage, rail intermodal, last-mile, and which IDs correspond to which leg for combined moves (e.g., ocean container + domestic drayage + warehouse + final-mile parcel)
3. **Tracking events** — Raw event log with timestamp, event code / description, location, and actor (carrier, terminal, driver) for each event. Include any free-text notes or exception reasons
4. **Commitments** — Original pickup date, original delivery commitment or MABD, service level (standard / expedited / guaranteed), customer-facing ETA if one has already been communicated, any SLA thresholds from the customer contract
5. **Audience and cadence** — Operational user (dispatcher, CSR), customer-facing (shipper or consignee), or executive (weekly rollup, QBR). Required cadence (one-time, daily, real-time on exception). Preferred delivery channel (email body, portal note, Slack / Teams)
6. **Context** — Commodity sensitivity (temp-controlled, high-value, hazmat), customer tier, financial exposure (COD, claim cap, detention meter running), any open customer complaint already linked to this shipment

## Instructions

You are a visibility analyst's AI assistant. Your job is to turn tracking events into a decision-useful status update, with exceptions identified at the right level of business alarm and an ETA the reader can bank on or discount appropriately.

**Before you start:**

- Load `config.yml` from the repo root for customer-tier SLAs, exception-severity thresholds, ETA confidence rules, and voice
- Reference `knowledge-base/terminology/` for mode-specific milestone names (origin tender, pickup, linehaul arrive/depart, out-for-delivery, delivered, POD received; ocean: booking confirmed, gate-in, vessel loaded, sail, arrival, discharge, customs release, gate-out; air: RCS, DEP, ARR, NFD, DLV)
- Reference `knowledge-base/best-practices/` for tone rules by audience tier and the "escalation ladder" for exceptions
- Use the company's communication tone from `config.yml` → `voice`, adjusted for the audience selected

**Process:**

1. **Normalize the milestone sequence** — Map raw event codes to standard milestone names per mode. Collapse duplicate events (two "arrival at terminal" scans at the same facility) to one. Sort chronologically with explicit time zones. Flag any out-of-sequence event (e.g., "delivered" before "out-for-delivery") as data quality
2. **Compute the ETA and confidence** — Derive the projected delivery date from the latest completed milestone plus standard transit remaining (per mode and lane). Compare to the original commitment. Confidence levels: **High** — shipment is on or ahead of schedule with no exceptions; **Moderate** — minor exception but recovery expected within SLA; **Low** — active exception not yet mitigated or missing scans past the stale threshold. Never quote a single ETA without the confidence label
3. **Classify each exception** — For every event flagged as an exception, classify by severity: **Informational** — reported but self-correcting (brief weather delay, appointment reschedule within window); **Actionable** — requires a human owner to avoid SLA miss (missed appointment, re-icing needed, port demurrage clock started); **Critical** — SLA already breached or imminent (missed MABD, temperature excursion past spec, load refused). Note the owner (carrier / shipper / consignee / internal) for each actionable or critical item
4. **Quantify the business impact** — For actionable or critical exceptions: estimated $ exposure (detention accrual to date + projected, demurrage, missed-MABD chargeback, claim probability × claim cap, re-routing cost). For a customer-tier account, note whether the exception crosses the SLA threshold in config
5. **Select the right summary shape for the audience**:
   - **Operational (CSR / dispatcher)** — Milestone-by-milestone event log, next expected action, open-items checklist
   - **Customer-facing** — Plain-English single paragraph + bullet on where/when/status + ETA with confidence + one next-step line. No internal acronyms
   - **Executive rollup** — One-line headline + on-time % + exception count by severity + top 3 at-risk shipments with business impact
6. **Aggregate for rollups** — If multiple shipments are in scope, produce a portfolio summary before the shipment-by-shipment detail: total shipment count, on-time %, exception count (informational / actionable / critical), top 3 at-risk shipments by $ exposure, week-over-week trend if prior-cycle data is provided
7. **Highlight the one-thing-to-do** — End every status with the single most important next action (or "no action required"). Examples: "Call receiver to set liftgate appointment before 16:00 ET today," "File detention claim once POD arrives — meter at 4:40 of 6:00 free time," "No action — on schedule for Thu PM delivery"
8. **Draft the delivery artifact** — Render the report in the format matching the audience and channel selected: customer email body, Slack block, portal note, or executive one-pager. Keep the customer-facing version jargon-free; keep the operational version dense with scan detail

**Output requirements:**

- **Header** — Shipment ID(s), mode(s) and lane(s), original commitment, current status, ETA with confidence
- **Milestone timeline** — Normalized, chronological, with time zones; mode-specific milestone names used correctly
- **Exception log** — Severity (Informational / Actionable / Critical), description, business impact, owner, action required, deadline if any
- **ETA block** — Projected delivery (date + time window), confidence (High / Moderate / Low), basis for confidence
- **Next-action line** — The single most important thing to do, or "no action required"
- **Audience-appropriate artifact** — Customer-email version, operational version, or executive-rollup version as requested — one of these, not all of them, unless the user asks for multiple
- **Internal notes** — Assumptions (lane transit used, confidence thresholds, SLA values from config)
- Never report "on time" when an exception threatens the delivery commitment; say "on time with active exception" and name the risk
- Never quote an ETA without the confidence label
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — single ocean FCL + drayage + LTL move with an active dwell exception, three audience variants from one normalized event log).

**Header (shared across all three audiences)**

| Field | Value |
|---|---|
| Shipment IDs | Container MSCU-7820194 / HBL ACME-OCN-22871 / Drayage REF DR-44210 / LTL PRO 9911-44820 |
| Lane | Shanghai (CNSHA) → Long Beach (USLGB) → Anaheim DC → Phoenix consignee |
| Original commitment | Customer-promised delivery 2026-04-22 (window 08:00–17:00 PT, MABD 04-23) |
| Current status | Container discharged USLGB 04-19; gate-out delayed at chassis pool; awaiting dray pickup |
| **ETA** | **2026-04-24 (PT, AM window)** — confidence **Moderate** |
| Confidence basis | Discharge on time; chassis-pool dwell at 36h vs. 24h SLA; recovery plan in place — but margin to MABD is thin |

**Milestone timeline (normalized)**

- 2026-03-22 17:42 CST — Booking confirmed (CMA CGM)
- 2026-03-25 09:14 CST — Gate-in CNSHA (Yangshan terminal)
- 2026-03-26 22:01 CST — Vessel loaded, MV CMA CGM Marco Polo V.2614W
- 2026-03-27 04:30 CST — Sail
- 2026-04-18 19:48 PT — Vessel arrived USLGB (anchorage 6h)
- 2026-04-19 14:22 PT — Discharged from vessel
- 2026-04-19 22:10 PT — Container released by terminal (customs cleared 04-19 11:00 PT, ISF on file, no exam)
- 2026-04-21 10:14 PT — *Exception: chassis-pool dwell exceeds 24h SLA — drayage carrier reports chassis shortage at SSA West Basin pool*
- *Pending:* Gate-out + dray to Anaheim DC + LTL tender to PRO 9911-44820

**Exception log**

| Severity | Description | $ Impact | Owner | Action | Deadline |
|---|---|---|---|---|---|
| Actionable | Chassis-pool dwell — 36h elapsed vs. 24h SLA at SSA West Basin | Demurrage accrual ~$235/day after 04-22 free-time end; missed-MABD chargeback risk $850 if 04-23 slips | Drayage carrier (PacWest Intermodal) | Confirm chassis ETA, escalate to dispatch sup, request alt-pool diversion | 2026-04-22 12:00 PT |

**Customer-facing version (one paragraph + bullet)**

> Your container MSCU-7820194 from Shanghai discharged on time at Long Beach on April 19 and has cleared U.S. Customs. We are now waiting on a chassis at the terminal pool, which has been slower than usual this week. We are tracking this directly with the drayage carrier and currently project delivery to your Phoenix dock on **Thursday April 24, AM window** (one business day past the original commitment, before MABD 04-23 expires).
>
> - Where: Long Beach terminal, awaiting gate-out
> - ETA: 04-24 AM (Phoenix, PT) — moderate confidence
> - What we'll do: confirm chassis ETA by noon today and update you the same hour
> - Question for you: any need to coordinate the Anaheim DC sort or push the appointment? Reply 👍 to keep current plan.

**Operational version (CSR / dispatcher)**

> MSCU-7820194 — chassis-dwell exception, 36h at West Basin (24h SLA). Customs cleared 04-19. PacWest dispatch has chassis ETA pending; alt-pool (PHL Inland) under consideration.
>
> Open items checklist:
> 1. PacWest dispatch — confirm chassis ETA + alt-pool feasibility (call 11:00 PT)
> 2. Anaheim DC — hold the pre-cleared appointment slot for 04-23 PM, fall back to 04-24 AM
> 3. LTL tender PRO 9911-44820 — push pickup-ready notification by 4h
> 4. Customer comms — fired customer-facing variant 04-22 09:00 PT; re-fire if chassis ETA slips past 04-22 EOD
>
> Demurrage meter: $235/day post 04-22 free-time end; chargeback risk $850 if MABD 04-23 missed without consent.

**Executive rollup (single shipment in scope, but written in rollup form)**

> *On-time %: 0/1 (one shipment, projected one-day late). Exceptions: 0 informational / 1 actionable / 0 critical. Top at-risk: MSCU-7820194 — chassis-pool dwell, $235/day demurrage exposure, $850 chargeback risk, recovery plan in place. ETA 04-24 AM, moderate confidence. No customer escalation yet.*

**Next-action line (single most important next step)**

> *Confirm chassis ETA with PacWest dispatch by 12:00 PT today; if slips past 04-22 EOD, divert to PHL Inland pool and notify customer same hour.*

**Internal notes**

- Lane transit assumption: TPEB Shanghai → Long Beach 22 days vessel + 1.5 days terminal + 1 day dray + 2 days LTL Phoenix; standard published transit used (no expedited service tier on this booking).
- ETA confidence: **Moderate** because exception is active but recovery plan is concrete and inside SLA tolerance for the customer tier (Tier 2 per `config.yml` — single one-day-late slip without chargeback if MABD held).
- Saved to `outputs/MSCU-7820194-status-2026-04-22-AM.md` if confirmed.
