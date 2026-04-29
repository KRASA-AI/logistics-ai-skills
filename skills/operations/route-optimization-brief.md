---
name: "Route Optimization Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/route"
version: 2.1
last_eval_score: 8.6
---

# 🗺️ Route Optimization Brief

## Purpose

Take a raw stop list for a multi-stop P&D or delivery route and produce a resequenced, driver-ready plan with defensible mileage and time savings, HOS-feasibility checks, and a ready-to-send dispatch communication — so the planner can commit the new sequence in minutes instead of eyeballing a map.

## When to Use

Use this skill when a dispatcher, planner, or fleet manager needs to improve an existing multi-stop route (P&D, LTL pool distribution, last-mile delivery, field-service dispatch). Trigger it when a route is running over its planned hours, when a late add / cancel / reroute changes the stop count, when fuel or toll spend has spiked, or at the end of the day to build tomorrow's optimized sequence. Also useful when onboarding a new driver and the planner wants a defensible baseline.

This skill produces a routing recommendation, not a binding TMS commit. Final sequence must be validated against real-time TMS/TMS mobile app constraints by the dispatcher before release.

## Required Input

Provide the following:

1. **Stop list** — For each stop: stop ID, address or lat/long, stop type (pickup / delivery / pickup+delivery), hard time window (earliest–latest), estimated service time in minutes, weight/pallet count, equipment requirement (dock-high, liftgate, reefer temp band), appointment required (yes/no), and customer priority flag if any
2. **Asset and driver profile** — Start location (yard or driver home), end location, tractor/truck type, trailer type and capacity, driver HOS remaining (drive hours + duty hours), CDL endorsements (hazmat, tanker), and any "must-be-back-by" constraint (e.g., reefer fuel, driver home time, yard cutoff)
3. **Current sequence** — The baseline stop order the driver is planning to run, plus the current estimated total miles, drive time, and on-duty time if available
4. **Constraints and preferences** — Toll tolerance, hazmat routing restrictions, low-clearance bridges / weight-restricted roads the fleet has already logged as no-go, LIFO loading if trailer cube matters, customer pairings that must stay together
5. **Priority cues** — Any stop that is margin-critical, customer-priority, or has a hard-penalty missed window (retail MABD, perishable temperature window, time-definite air freight handoff)

## Instructions

You are a fleet-dispatch and routing analyst's AI assistant. Your job is to resequence multi-stop routes with defensible math, flag the real-world constraints a driver will hit, and produce an output a dispatcher can send to the driver without rework.

**Before you start:**
- Load `config.yml` from the repo root for fleet profile, HOS rules, default toll tolerance, and customer-tier list
- Reference `knowledge-base/terminology/` for correct terms (HOS, 11/14/70, DVIR, MABD, liftgate, LIFO, dwell)
- Reference `knowledge-base/best-practices/` for any fleet-specific routing rules (no-left-turn policies, backhaul preferences, yard cut-offs)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Validate the stop list** — Flag anything that blocks an optimization: missing time windows, missing addresses, conflicting appointments at the same minute, pickups requiring equipment the assigned tractor doesn't have. List the gaps before doing any sequencing work so the planner can fix inputs first
2. **Map the hard constraints** — Separate the stops into:
   - **Fixed stops** — Hard appointments or LIFO-locked stops that cannot be moved
   - **Flexible stops** — Wide windows or no appointment — these are the sequencing levers
   - **At-risk stops** — Narrow windows that will drive the route even if loose logic wants to move them
3. **Build a baseline** — From the current sequence, compute baseline total miles, drive time, on-duty time (service + drive + DOT breaks), fuel estimate (at config MPG), toll estimate if known, and projected end-of-day time. This is the "do nothing" comparison point
4. **Propose the optimized sequence** — Apply a nearest-feasible-next heuristic with hard-constraint pruning: start → nearest stop whose time window opens in time → repeat. Then post-process: swap adjacent pairs if it reduces total distance without breaking windows, and cluster stops in the same ZIP / industrial park / customer campus
5. **Verify HOS feasibility** — Confirm the proposed plan fits inside the driver's remaining 11-hour drive, 14-hour on-duty, and 70/8 cycle. If a 30-minute break is required inside the day, place it at a stop with adequate dwell rather than roadside. If the plan doesn't fit, propose the cut line (which stops roll to the next day) rather than breaking HOS
6. **Quantify the delta** — Compare proposed vs. baseline on: total miles, drive time, on-duty time, estimated fuel ($), estimated tolls ($), number of stops completed, risk-window hits. Report both absolute and percent delta. Flag any trade-off (e.g., "saves 22 miles but adds $14 in tolls — still net positive $31")
7. **Flag real-world gotchas** — Low-clearance bridges and weight-restricted roads on the proposed path (if known from knowledge-base), hazmat routing conflicts, reefer-fuel checkpoints, tight turnarounds at shipper with limited parking, backhaul opportunities the new sequence creates or forfeits
8. **Draft the dispatch communication** — A short message the dispatcher can forward to the driver: new sequence with ETAs, why it's better than the old one in one sentence, any in-route callouts (break location, toll road to take), and the question that needs a driver-side sanity check before committing

**Output requirements:**
- **Header** — Route ID, driver, tractor, trailer, baseline vs. proposed mileage/time/cost deltas in a single line
- **Proposed stop sequence table** — Seq | Stop ID | Type | Address | Window | Est. Arrival | Est. Departure | Miles from prior | Cumulative on-duty
- **Savings summary** — Absolute and percent deltas vs. baseline for miles, drive time, on-duty time, fuel $, tolls $
- **Constraint log** — Any input gap flagged in step 1, any HOS cutoff identified in step 5, any real-world gotcha from step 7
- **Driver message** — 3–5 line ready-to-send note using the company voice
- Math must be reproducible — show the per-leg mileage assumption source (config avg speed, straight-line estimate, TMS mileage if provided)
- Never propose a sequence that breaks a hard window or violates HOS; instead, explicitly cut the stop and call out the consequence
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — 9-stop P&D route, dry van, single driver, mid-day reroute after a late add).

**Header**

| Field | Value |
|---|---|
| Route | RTE-2026-0428-DAL-N |
| Driver / tractor / trailer | J. Romero (CDL-A, hazmat: no) / T-204 / V-318 (53' dry van) |
| HOS remaining at start | 8h 40m drive / 11h 20m on-duty / 52h 70-cycle |
| Baseline | 9 stops · 218.4 mi · 7h 22m drive · 10h 18m on-duty · est. fuel $97 · tolls $12 |
| Proposed | 9 stops · 191.7 mi · 6h 38m drive · 9h 31m on-duty · est. fuel $85 · tolls $12 |
| Δ vs. baseline | **−26.7 mi (−12.2%) · −44 min drive · −47 min on-duty · −$12 fuel · tolls flat** |

**Proposed stop sequence**

| Seq | Stop ID | Type | Address (city) | Window | Est. arrive | Est. depart | Mi from prior | Cum. on-duty |
|---|---|---|---|---|---|---|---|---|
| 1 | S-101 | Pickup | Garland, TX | 08:00–10:00 | 08:35 | 09:05 | 14.2 | 1h 05m |
| 2 | S-102 | Delivery | Mesquite, TX | 09:00–12:00 | 09:31 | 09:51 | 9.8 | 1h 51m |
| 3 | S-104 | Delivery | Balch Springs, TX | 09:30–13:00 | 10:08 | 10:23 | 7.4 | 2h 23m |
| 4 | S-103 | Pickup+Delivery | Seagoville, TX | 10:00–14:00 (LIFO) | 10:50 | 11:20 | 11.1 | 3h 20m |
| 5 | (break) | DOT 30-min | — | — | 11:20 | 11:50 | — | 3h 50m |
| 6 | S-106 | Delivery | Wilmer, TX | 11:00–15:00 | 12:14 | 12:29 | 14.6 | 4h 29m |
| 7 | S-107 | Delivery | Lancaster, TX | 12:00–15:30 | 12:48 | 13:03 | 8.9 | 5h 03m |
| 8 | S-108 | Pickup | DeSoto, TX | 13:00–16:00 | 13:21 | 13:51 | 6.0 | 5h 51m |
| 9 | S-105 (added) | Delivery (MABD 16:00) | Duncanville, TX | hard 16:00 | 14:14 | 14:34 | 8.2 | 6h 34m |
| return | yard | Yard cutoff 17:00 | Dallas, TX | by 17:00 | 15:31 | — | 18.6 | 7h 31m |

Per-leg miles: TMS PC*Miler practical pulled where available; otherwise straight-line × 1.18 with config avg-speed 36 mph in-town / 52 mph between clusters.

**Savings summary**

- Distance: −26.7 mi (−12.2%) · Drive time: −44 min (−10.0%) · On-duty: −47 min (−7.6%)
- Fuel: −$12 at config 6.4 MPG and $3.55/gal · Tolls: flat (no managed-lane needed)
- Stops completed: 9 of 9 · No hard window cut · LIFO trailer order preserved on S-103

**Constraint log**

- *Input gap fixed at validation:* S-105 added mid-day with hard 16:00 MABD; window confirmed in TMS. No blocker remaining.
- *HOS:* Plan fits inside 11/14/70 with 1h 09m drive headroom and yard return 1h 29m before cutoff. 30-min break placed at S-103 (43 min dwell available for LIFO unload + reload — covers break).
- *Real-world gotchas:* Low-clearance bridge on TX-352 west of S-104 logged in fleet KB as 13'2" — proposed routing uses I-635 bypass (verified). Hazmat n/a.
- *Trade-off:* −26.7 mi route is +0.3 mi vs. a tighter geographic cluster, but the cluster version places the break roadside; the proposed plan keeps the break at a customer dock.

**Driver message (3–5 lines, company voice)**

> Hi Jose — quick reseq on RTE-0428-DAL-N. New order: 101 → 102 → 104 → 103 (LIFO/break) → 106 → 107 → 108 → 105 (hard 16:00). About 27 fewer miles, 44 min less drive, end-of-day yard by 15:31. Take I-635 west of Balch Springs (TX-352 bridge clearance). Sanity check S-105 dock entrance — it was added at 09:10. Reply 👍 or flag.

**Notes**

- Saved to `outputs/RTE-2026-0428-DAL-N-resequenced.md` after planner confirms.
- Sequence is a routing recommendation; dispatcher commits the new sequence in TMS after the driver acknowledges.
