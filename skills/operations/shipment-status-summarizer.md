---
name: "Shipment Status Summarizer"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~10 min/update"
version: 2.2
last_eval_score: 8.7
---

# 📦 Shipment Status Summarizer

## Purpose

Take raw tracking events from one or more modes (parcel, LTL, TL, ocean, air, drayage, last-mile) and produce a concise, audience-appropriate status update that normalizes the milestone language, highlights exceptions with their business impact, states a confident or caveated ETA, and surfaces the one or two things that actually need a human decision — so the CSR, account manager, or executive reader gets signal, not a log dump.

## When to Use

Use this skill for daily "where's my freight" questions, for proactive customer updates on monitored shipments, for executive-level multi-shipment rollups ("how's our inbound this week"), for account-review prep (on-time %, exception count, claim exposure), and any time a tracking report from a carrier, portal, or visibility platform (Project44, FourKites, MacroPoint, SONAR, Flexport) needs to be converted into something a non-logistics reader can use.

It produces a report, not a correction. If an exception requires a dispatcher or carrier action (appointment reset, recovery plan, re-icing), the skill flags the action but does not execute it — route that to the exception handler.

## Required Input

This skill runs in one of three **modes**. Pick the mode from what the user actually has in hand — do not ask for the full input package when the question is a single-shipment "where is it." The mode gate is the difference between a 20-second answer and a stalled request.

**Mode selection rule:** if the user supplies only IDs and an event log → **Quick Lookup**. If they name an audience or a commitment → **Standard**. If more than one shipment is in scope, or the ask is a rollup / on-time % / account-review number → **Rollup**. If the user states a mode, honor it. State the mode you ran in the header of every output.

### Mode A — Quick Lookup (minimum viable input: items 1 and 3)

The default for a fast "where's my freight" from a dispatcher or CSR. Run with only:

1. **Shipment identifier(s)** — Load # / BOL # / Pro # / container # / MAWB / HAWB / tracking # (list all relevant IDs for multi-leg moves)
3. **Tracking events** — Raw event log with timestamp, event code / description, location, and actor (carrier, terminal, driver) for each event. Include any free-text notes or exception reasons

Everything else is **inferred or defaulted, never asked for**: mode is inferred from the ID format and event vocabulary (container # + gate-in/discharge → ocean; PRO # + terminal scans → LTL; MAWB + RCS/DEP/ARR → air); the commitment falls back to `status.default_transit` for the lane and mode; the audience falls back to `status.default_audience`; the customer tier and SLA fall back to `status.customer_tiers` keyed on the account if the account is named, or to the default tier if it is not. Produce the full output with an explicit **Inferred-inputs block** listing each inference and the one input that, if supplied, would most improve the answer. Do not withhold the summary pending that input.

### Mode B — Standard status update (Quick Lookup + items 2, 4, 5, 6 as available)

Use when the update is customer-facing, or when a commitment or SLA is in play. Any of the following that the user supplies overrides the Mode A default; any they omit is defaulted exactly as in Mode A and disclosed in the Inferred-inputs block:

2. **Mode(s) and legs** — Parcel, LTL, TL, ocean (FCL/LCL), air, drayage, rail intermodal, last-mile, and which IDs correspond to which leg for combined moves (e.g., ocean container + domestic drayage + warehouse + final-mile parcel)
4. **Commitments** — Original pickup date, original delivery commitment or MABD, service level (standard / expedited / guaranteed), customer-facing ETA if one has already been communicated, any SLA thresholds from the customer contract
5. **Audience and cadence** — Operational user (dispatcher, CSR), customer-facing (shipper or consignee), or executive (weekly rollup, QBR). Required cadence (one-time, daily, real-time on exception). Preferred delivery channel (email body, portal note, Slack / Teams)
6. **Context** — Commodity sensitivity (temp-controlled, high-value, hazmat), customer tier, financial exposure (COD, claim cap, detention meter running), any open customer complaint already linked to this shipment

### Mode C — Portfolio rollup (Standard, across N shipments)

Use for executive rollups and account reviews. Requires items 1 and 3 per shipment plus the scope definition (account, lane set, or date window) and, if a trend line is wanted, the prior-cycle rollup. Missing per-shipment commitments default per Mode A rather than dropping the shipment from the rollup — a shipment with an unknown commitment is counted and flagged **commitment-unknown**, never silently excluded from the on-time %.

**Ask-only-if-blocking rule:** ask a clarifying question *only* when the answer would change the direction of the recommendation and no config default exists to stand in — in practice, only when the event log is missing entirely, or when a customer-facing artifact is requested for an account whose tier is unknown and whose SLA drives whether the exception is Critical. Everything else is defaulted and disclosed, not asked.

## Configuration Reference

- `config.yml` — `status.default_audience` (audience shape used when the request does not name one, step 5), `status.customer_tiers` (account → tier → SLA thresholds and chargeback terms, driving the step-3 severity call and the step-4 exposure math when the request omits the tier), `status.exception_severity` (per-exception-type thresholds that set the Informational / Actionable / Critical waterline in step 3 — dwell hours, missing-scan staleness, temperature-excursion tolerance), `status.eta_confidence_rules` (the dwell / staleness / open-exception cutoffs that map a shipment to High / Moderate / Low in step 2, so the confidence label is a repo constant rather than a per-run judgment call), `status.default_transit` (published lane + mode transit table used to derive the ETA when the request supplies no commitment), `status.stale_scan_threshold` (hours without a scan before the shipment is flagged data-quality in step 1 and confidence is capped at Low in step 2), `status.accrual_rates` (per-diem demurrage / detention / chargeback defaults priced into step 4 when the request does not state them), `status.owner_directory` (carrier / terminal / internal owner per exception type, pre-filling the step-3 owner column), `voice` (tone, adjusted per audience). An input-supplied value always overrides the matching config default, and every config-sourced value is surfaced in the internal-notes block labeled *config* vs. *supplied*.
- Knowledge base — `knowledge-base/terminology/` (mode-specific milestone names, EDI 214 status codes, EDI 856 ASN references), `knowledge-base/best-practices/` (tone rules by audience tier and the exception escalation ladder supplementing `status.exception_severity`)
- Sibling skills — `skills/operations/shipment-exception-handler.md` (owns the corrective action this skill only flags), `skills/customer-service/shipment-inquiry-responder.md` (owns the inbound-customer reply this skill's customer-facing artifact feeds), `skills/operations/carrier-performance-scorecard.md` (consumes the Mode C rollup on-time % and exception counts)

## Instructions

You are a visibility analyst's AI assistant. Your job is to turn tracking events into a decision-useful status update, with exceptions identified at the right level of business alarm and an ETA the reader can bank on or discount appropriately.

**Before you start:**

- **Select the mode** (Quick Lookup / Standard / Rollup) per the mode-selection rule above, and load the `status.*` config-defaults profile named in the Configuration Reference. Every input the selected mode does not require is filled from that profile — never asked for — and disclosed in the Inferred-inputs block
- Reference `knowledge-base/terminology/` for mode-specific milestone names (origin tender, pickup, linehaul arrive/depart, out-for-delivery, delivered, POD received; ocean: booking confirmed, gate-in, vessel loaded, sail, arrival, discharge, customs release, gate-out; air: RCS, DEP, ARR, NFD, DLV)
- Reference `knowledge-base/best-practices/` for tone rules by audience tier and the "escalation ladder" for exceptions
- Use the company's communication tone from `config.yml` → `voice`, adjusted for the audience selected (or `status.default_audience` if none was named)

**Process:**

1. **Normalize the milestone sequence** — Map raw event codes to standard milestone names per mode; where the mode was not stated, infer it from the ID format and event vocabulary and record the inference. Collapse duplicate events (two "arrival at terminal" scans at the same facility) to one. Sort chronologically with explicit time zones. Flag any out-of-sequence event (e.g., "delivered" before "out-for-delivery") as data quality, and flag any gap exceeding `status.stale_scan_threshold` as a missing-scan data-quality item
2. **Compute the ETA and confidence** — Derive the projected delivery date from the latest completed milestone plus standard transit remaining — from the stated commitment, or from `status.default_transit` for the lane and mode when no commitment was supplied (label it *config*). Compare to the original commitment. Apply `status.eta_confidence_rules` so the label is a repo constant, not a per-run judgment: **High** — shipment is on or ahead of schedule with no exceptions; **Moderate** — minor exception but recovery expected within SLA; **Low** — active exception not yet mitigated, or missing scans past `status.stale_scan_threshold`. Never quote a single ETA without the confidence label
3. **Classify each exception** — For every event flagged as an exception, classify by severity against the `status.exception_severity` thresholds (dwell hours, staleness, temperature tolerance) rather than by feel: **Informational** — reported but self-correcting (brief weather delay, appointment reschedule within window); **Actionable** — requires a human owner to avoid SLA miss (missed appointment, re-icing needed, port demurrage clock started); **Critical** — SLA already breached or imminent (missed MABD, temperature excursion past spec, load refused). Name the owner (carrier / shipper / consignee / internal) for each actionable or critical item, defaulting from `status.owner_directory` by exception type when the request does not name one
4. **Quantify the business impact** — For actionable or critical exceptions: estimated $ exposure (detention accrual to date + projected, demurrage, missed-MABD chargeback, claim probability × claim cap, re-routing cost), priced from the request's stated figures or, failing that, from `status.accrual_rates`. Resolve the account's tier and SLA from `status.customer_tiers` when the request omits it, and note whether the exception crosses that tier's SLA threshold
5. **Select the right summary shape for the audience** (the stated audience, else `status.default_audience`):
   - **Operational (CSR / dispatcher)** — Milestone-by-milestone event log, next expected action, open-items checklist
   - **Customer-facing** — Plain-English single paragraph + bullet on where/when/status + ETA with confidence + one next-step line. No internal acronyms
   - **Executive rollup** — One-line headline + on-time % + exception count by severity + top 3 at-risk shipments with business impact
6. **Aggregate for rollups** — If multiple shipments are in scope, produce a portfolio summary before the shipment-by-shipment detail: total shipment count, on-time %, exception count (informational / actionable / critical), top 3 at-risk shipments by $ exposure, week-over-week trend if prior-cycle data is provided
7. **Highlight the one-thing-to-do** — End every status with the single most important next action (or "no action required"). Examples: "Call receiver to set liftgate appointment before 16:00 ET today," "File detention claim once POD arrives — meter at 4:40 of 6:00 free time," "No action — on schedule for Thu PM delivery"
8. **Draft the delivery artifact** — Render the report in the format matching the audience and channel selected: customer email body, Slack block, portal note, or executive one-pager. Keep the customer-facing version jargon-free; keep the operational version dense with scan detail

**Output requirements:**

- **Header** — Run mode (Quick Lookup / Standard / Rollup), shipment ID(s), mode(s) and lane(s), original commitment, current status, ETA with confidence
- **Milestone timeline** — Normalized, chronological, with time zones; mode-specific milestone names used correctly
- **Exception log** — Severity (Informational / Actionable / Critical), description, business impact, owner, action required, deadline if any
- **ETA block** — Projected delivery (date + time window), confidence (High / Moderate / Low), basis for confidence
- **Next-action line** — The single most important thing to do, or "no action required"
- **Audience-appropriate artifact** — Customer-email version, operational version, or executive-rollup version as requested — one of these, not all of them, unless the user asks for multiple
- **Inferred-inputs block** — Every field the run defaulted rather than received, labeled *inferred* (derived from the event log) or *config* (from `status.*`), plus the single input that would most improve the answer if supplied. Required on every Quick Lookup run and on any Standard/Rollup run with omitted inputs
- **Internal notes** — Assumptions (lane transit used, confidence thresholds, SLA values), each labeled *config* vs. *supplied*
- Never report "on time" when an exception threatens the delivery commitment; say "on time with active exception" and name the risk
- Never quote an ETA without the confidence label
- Never stall a Quick Lookup run to ask for an input that has a config default — default it, disclose it, and deliver the summary
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — single ocean FCL + drayage + LTL move with an active dwell exception, three audience variants from one normalized event log). Run in **Standard** mode: the requester supplied IDs, events, and the commitment, but named no audience and no accrual rates — both defaulted from `status.*` and disclosed in the Inferred-inputs block below.

**Header (shared across all three audiences)**

| Field | Value |
|---|---|
| Run mode | **Standard** (IDs + events + commitment supplied; audience and accrual rates defaulted from config) |
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

**Inferred-inputs block**

| Field | Value used | Source |
|---|---|---|
| Audience | Operational (CSR / dispatcher), with customer-facing variant rendered on request | *config* — `status.default_audience` (request named no audience) |
| Customer tier / SLA | Tier 2 — one-day-late slip tolerated without chargeback if MABD held | *config* — `status.customer_tiers` keyed on the Acme account (request omitted tier) |
| Demurrage accrual | $235/day after free-time end | *config* — `status.accrual_rates` → `demurrage.uslgb` (request stated no rate) |
| Missed-MABD chargeback | $850 | *config* — `status.accrual_rates` → `chargeback.tier_2` |
| Dwell severity waterline | 24h chassis-pool SLA → Actionable at 24h, Critical at 48h | *config* — `status.exception_severity` → `chassis_dwell` |
| Exception owner | Drayage carrier (PacWest Intermodal) | *config* — `status.owner_directory` → `chassis_dwell` (request named no owner) |
| Mode / legs | Ocean FCL → drayage → LTL | *inferred* from ID formats (container # + HBL, dray REF, PRO #) and the gate-in / discharge / terminal-scan event vocabulary |

> **Most valuable missing input:** the drayage carrier's chassis ETA. Supplying it would move ETA confidence off **Moderate** — it is the single unknown holding the 04-24 projection open.

**Quick Lookup variant (Mode A — same shipment, IDs + events only)**

> **MSCU-7820194 — Mode A (Quick Lookup).** Discharged USLGB 04-19, customs cleared, awaiting gate-out. **Chassis-pool dwell 36h** (Actionable — config waterline 24h). **ETA 04-24 AM PT, confidence Moderate.** Next action: confirm chassis ETA with the drayage carrier today.
>
> *Inferred:* ocean+dray+LTL from ID formats · *Config:* Tier-2 SLA, $235/day demurrage, 24h dwell waterline, default audience. *Supply the chassis ETA to firm the confidence.*

**Internal notes**

- Lane transit assumption: TPEB Shanghai → Long Beach 22 days vessel + 1.5 days terminal + 1 day dray + 2 days LTL Phoenix (*config* — `status.default_transit`, standard published transit; no expedited service tier on this booking).
- ETA confidence: **Moderate** — active exception with a concrete recovery plan, inside the Tier-2 tolerance (*config* — `status.customer_tiers`) and below the Critical waterline (*config* — `status.exception_severity`). Commitment and event log *supplied*; audience, accrual rates, tier, and owner *config*.
- Saved to `outputs/MSCU-7820194-status-2026-04-22-AM.md` if confirmed.
