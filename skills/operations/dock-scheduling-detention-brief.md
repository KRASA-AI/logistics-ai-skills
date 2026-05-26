---
name: "Dock Scheduling & Detention Prevention Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/brief"
version: 1.1
last_eval_score: 8.8
---

# 🏭 Dock Scheduling & Detention Prevention Brief

## Purpose

Analyze dock appointment data, carrier arrival patterns, and detention/demurrage invoices to produce an action-ready brief that reshapes the daily dock schedule, cuts avoidable detention charges, and protects customer-critical inbound/outbound flows.

## When to Use

Use this skill when detention and demurrage spend is trending up, when warehouse throughput is falling behind order cadence, when a retailer charges back for late deliveries on their receiving schedule, or when planning a dock-door reallocation (e.g., shifting doors between inbound and outbound as season changes). It is also useful before weekly ops huddles to surface the 3–5 schedule changes that will deliver the most relief.

## Required Input

Provide the following:

1. **Dock appointment log** — Scheduled vs. actual arrival, dwell time per door, appointment type (live load, drop, cross-dock), carrier, commodity, and any no-show or late-arrival flags
2. **Detention/demurrage invoices** — Dollar amounts, carriers billing the charges, shipment identifiers, and claimed start/stop times so you can confirm whether each charge is legitimate vs. disputable
3. **Facility constraints** — Number of doors, staffed hours, forklift/labor capacity by shift, any customer-specific appointment rules (e.g., retailer MABD windows)
4. **Priority cues** — Strategic customers, high-margin lanes, or perishable/temperature-controlled freight that must not slip

## Instructions

You are a warehouse and yard operations manager's AI assistant. Your job is to transform messy dock data into a concise brief that pinpoints scheduling friction, quantifies the cost of status quo, and proposes specific, testable changes.

**Before you start:**
- Load `config.yml` from the repo root for facility profile, SLA tiers, and any customer-specific receiving rules
- Reference `knowledge-base/terminology/` for correct terms (detention vs. demurrage, MABD, dwell time, live load vs. drop-hook)
- Reference `knowledge-base/regulations/` if hours-of-service or hazmat staging rules apply
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Profile the appointment book** — Bucket appointments by hour-of-day, day-of-week, appointment type, and carrier. Flag chronic choke points (e.g., Monday 0700–0900 is 140% of dock capacity; Friday 1500+ is under-utilized)
2. **Quantify the detention exposure** — Segment detention dollars by: carrier-caused (late arrival, no-show), facility-caused (labor shortage, door blocked), customer-caused (late release of BOL, receiving refusal), and shared/ambiguous. Separate legitimate from disputable charges based on contractual free-time, ELD-backed timestamps, and gate records
3. **Identify the top friction drivers** — Rank the top 5 root causes by dollar impact and by frequency. Typical categories include: over-booked early-morning slots, live-load commodities scheduled on drop doors, carriers missing appointments without penalty, BOL/seal prep lagging the driver
4. **Propose schedule changes** — For each friction driver, recommend one concrete change (e.g., "Shift 30% of Monday 0700 inbound to 1000–1400 slots; reward carriers with guaranteed unload within 30 min of appointment"). Estimate the dollar and throughput impact
5. **Draft carrier and customer communications** — Produce ready-to-send messages:
   - **Carriers** — New slot policy, free-time reminder, any disputable detention notifications
   - **Customer receiving teams** — Updated MABD expectations if you are the shipper; updated appointment commitments if you are the 3PL/warehouse
   - **Internal ops team** — Staffing and door-assignment adjustments for the next 2 weeks
6. **Set measurement checkpoints** — Define the KPIs to watch (avg. dwell time, detention $ per 100 appointments, on-time appointment %) and the review cadence (e.g., 2-week pilot review)

**Output requirements:**
- One-page executive brief at the top, followed by supporting detail
- Every recommendation tied to a specific dollar or throughput impact estimate
- Disputable detention line items called out with the evidence needed to challenge them
- No generic "improve communication" recommendations — every action is specific and assignable
- Correct industry terminology (detention vs. demurrage, accessorial, dwell, turn-time)
- Saved to `outputs/` if the user confirms

## Example Output

**Scenario:** 14-door, single-shift cross-dock in Greer, SC (Tier-2 facility per `config.yml`) servicing a mix of OEM inbound + retail outbound. April detention spend ran $14,820 on 612 inbound appointments (15.4% no-show or late-arrival rate); the OEM customer is threatening to chargeback for two missed primary-tender lanes the prior week. Run requested for the 2026-05-26 weekly ops huddle, covering the four weeks 2026-04-27 → 2026-05-24. Input: appointment log + detention invoice register + facility constraints + the OEM and retail priority cues.

---

**Executive brief (one page)**

> Greer cross-dock ran $14,820 in detention charges across 612 inbound appointments in the past 4 weeks (avg $24/appt; 41% of charges are disputable on ELD-backed timestamps). The top friction driver is Monday 06:30–08:30 over-bookings at **148% of dock-staffed capacity** while Friday 14:00–17:00 runs at 38% — a window-slide of 30% of the Monday-AM load to Friday-PM yields **~$5,800/cycle in avoided detention** plus restores the OEM primary-tender on-time rate from 88% to a projected 96%+. Disputable detention totals **$6,074** across 9 invoices where the carrier billed past the 2-hour free-time window but the ELD shows the driver arrived before the appointment and the BOL release timestamp lagged — these are filed back to the carriers this week with the gate-log evidence. **Three named schedule changes proposed below**, each with dollar impact and a 2-week pilot review on 2026-06-09.

**Detention exposure — segmented by cause**

| Cause | $ Last 4 wks | % of total | Disputable $ | Notes |
|---|---|---|---|---|
| Carrier-caused (late arrival, no-show) | $4,210 | 28% | $0 | Late-arrival penalties under carrier-side contractual terms; not disputable |
| **Facility-caused (door blocked, labor short, BOL lag)** | **$8,535** | **58%** | **$6,074** | **$6,074 disputable on ELD evidence (driver arrived on time, BOL release lagged); $2,461 legitimate facility detention (forklift driver out wk of 05-11)** |
| Customer-caused (receiving refusal, late release) | $1,420 | 10% | $0 | Two OEM-side late BOL releases on 05-06 and 05-13; billed back to OEM under master agreement clause §4.3 |
| Shared / ambiguous | $655 | 4% | $0 | Three appointments with no ELD record (manual logbook driver) — not disputable without ELD evidence |
| **Total** | **$14,820** | **100%** | **$6,074 (41%)** | |

**Top 5 friction drivers (ranked by $ impact + frequency)**

| # | Friction driver | $ Impact / 4 wks | Frequency | Fix proposed |
|---|---|---|---|---|
| 1 | **Monday 06:30–08:30 over-booking at 148% capacity** | $4,820 (32% of detention) | 4 of 4 Mondays | Slide 30% of Monday-AM inbound to Friday-PM (~$5.8K avoided/cycle) |
| 2 | **BOL/seal prep lagging driver arrival** (avg 38 min lag) | $3,210 (22%) | 27 occurrences | Pre-stage BOLs at the shift start; pilot with the 06:00 outbound cluster |
| 3 | **Live-load commodities scheduled on drop doors 4, 7, 9** (cross-traffic) | $2,440 (16%) | 18 occurrences | Re-designate doors 4 & 7 as live-load-only Mon/Wed/Fri; doors 9 stays drop |
| 4 | **No-show carrier penalty not enforced** (carrier-side, repeat offenders) | $1,560 (11%) | 9 no-shows | Send Notice-of-Repeat-No-Show letter to the two repeat carriers; trigger penalty clause §6.2 |
| 5 | **Forklift driver coverage gap wk of 05-11** | $1,250 (8%) | 1 week | Cross-train two checkers as backup forklift drivers; complete by 06-30 |

**Proposed schedule changes**

1. **Slide Monday-AM inbound — 30% of 06:30–08:30 appointments to Friday 14:00–17:00.** Owner: M. Reyes (yard coordinator). Timeline: new schedule posted in the appointment portal by 2026-05-28 EOD, effective Monday 2026-06-01. Estimated impact: $5,800/cycle avoided detention + restores OEM primary-tender on-time from 88% → projected 96%+; protects against the OEM chargeback risk on the next two primary tenders. Pilot review 2026-06-09; if on-time rate < 94% rollback to the prior schedule. *No incremental labor cost — same staff hours, reallocated across the week.*
2. **Re-designate doors 4 and 7 as live-load-only on Mon / Wed / Fri.** Owner: J. Park (shift supervisor). Timeline: dock-door schematic updated by 2026-05-29 EOD; gate-house notified same day. Estimated impact: $2,440/cycle avoided cross-traffic detention + 12 min average dwell reduction on live-load commodities. Pilot review 2026-06-09. *No incremental cost.*
3. **Pre-stage BOLs at shift start — pilot on the 06:00 outbound cluster only.** Owner: T. Nguyen (shipping clerk lead). Timeline: SOP updated by 2026-05-29 EOD; pilot starts 2026-06-01. Estimated impact: $1,600/cycle (50% of the $3,210 BOL-lag detention — proportional to the cluster's share of total outbound) + 25 min average lag reduction on the 06:00 cluster. Pilot review 2026-06-09; expand to all clusters if BOL-lag drops below 15 min. *No incremental cost — workflow re-sequencing only.*

**Ready-to-send messages**

*To the two repeat-no-show carriers (template — fill carrier name and load IDs):*

> Subject: Notice of repeat-no-show appointments — [Carrier] — [Period]
>
> Per the master transportation agreement §6.2 (No-Show and Late-Arrival Penalties), the following appointments at our Greer, SC cross-dock were no-show or > 2 hours late without same-day notice in the past 4 weeks:
>
> 1. [PRO #] — [Date / appointment window] — no-show
> 2. [PRO #] — [Date / appointment window] — 3 hr 14 min late, no notice
> 3. [PRO #] — [Date / appointment window] — no-show
>
> Per §6.2, the per-occurrence penalty of $150 applies; aggregate $450 will be deducted from the next remit cycle (2026-06-05 settlement). To avoid future deductions, please notify our dispatch desk at least 60 minutes ahead of any missed window. Repeat occurrences after this notice may trigger a routing-guide suspension review.
>
> Dispatch desk: (864) 555-0184 | dispatch@[company].com | weekly hours 06:00–18:00 ET.

*To the OEM customer's receiving / inbound logistics team (BOL-lag callback):*

> Subject: Greer cross-dock — Monday inbound schedule change effective 2026-06-01
>
> We are sliding 30% of our Monday-AM inbound window (06:30–08:30) to Friday-PM (14:00–17:00) effective 2026-06-01 to restore primary-tender on-time rates after a 4-week run at 88%. The shift releases 4 dock-hours of Monday capacity that we will reserve for your OEM primary lanes; we will hold your two missed primary tenders from wk of 05-13 against the schedule-change effective date and re-tender by 2026-06-05. No action required on your end; please flag any receiving-window dependencies your team has with us by 2026-05-29 EOD so we can lock the schedule.
>
> Two OEM-side BOL releases on 05-06 and 05-13 ran past our 2-hour free-time window (the detention is being billed back under master agreement §4.3 — separate invoice 2026-06-01). Happy to walk through the timestamps on a 15-min call this week.
>
> M. Reyes — yard coordinator, Greer SC

*To the internal ops team (staffing and door-assignment for the next 2 weeks):*

> Subject: Greer dock — 2-week schedule changes effective Monday 06-01
>
> Three changes going live Monday 2026-06-01 (2-week pilot, review 2026-06-09):
>
> 1. **Monday inbound slide** — 30% of 06:30–08:30 appts moved to Friday 14:00–17:00. Door assignments in the portal; gate-house list reprinted Friday 05-29.
> 2. **Doors 4 & 7 live-load-only Mon/Wed/Fri.** Schematic updated 05-29; door signs swapped before 06:00 Monday.
> 3. **Pre-staged BOLs for the 06:00 outbound cluster.** New SOP in the shared drive; T. Nguyen owns. Cross-train two checkers (P. Akande, L. Cho) as backup forklift drivers by 06-30 to close the wk-of-05-11 coverage gap.
>
> Disputable detention claw-back ($6,074 across 9 invoices) being sent to carriers this week with ELD + gate-log evidence per the SOP. Filed under outputs/detention-disputes-2026-05-26/.

**Measurement checkpoints (2-week pilot review 2026-06-09)**

| KPI | Baseline (last 4 wks) | Target (2-wk pilot) | Trip-wire (rollback) |
|---|---|---|---|
| Avg dwell time (inbound, live-load) | 84 min | ≤ 65 min | > 90 min |
| Detention $ per 100 appts | $242 | ≤ $150 | > $260 |
| On-time appointment % (OEM primary lanes) | 88% | ≥ 96% | < 94% |
| Mon 06:30–08:30 door utilization | 148% | 105–115% | > 130% sustained |
| Friday 14:00–17:00 door utilization | 38% | 65–85% | < 50% (under-fill) |

**Internal notes**

- **Snapshots:** appointment log pulled 2026-05-25 EOD ET (TMS appointments module, 4-wk window); detention invoice register pulled 2026-05-25 EOD ET (AP module + carrier-portal cross-check); ELD timestamps verified on the 9 disputable invoices via carrier-portal pulls 2026-05-25 18:00–20:00 ET.
- **Assumptions made:** (1) OEM master agreement §4.3 (customer-caused detention billed back to OEM) and §6.2 (carrier no-show penalty $150/occurrence) are the controlling clauses — confirmed against the 2026-01-15 contract redline. (2) The Friday 14:00–17:00 window has the dock-staffed capacity to absorb 30% of the Monday-AM slide (verified against the shift schedule and the gate-house door-utilization rolling 4-wk). (3) The two missed OEM primary tenders are held under the schedule-change clause, not the force-majeure clause — if the OEM disputes, the fallback is the standard routing-guide-secondary at +12% cost (covered in the OEM's annual freight budget per `config.yml`).
- **Saved to** `outputs/dock-scheduling-brief-greer-2026-05-26.md` (if confirmed).
