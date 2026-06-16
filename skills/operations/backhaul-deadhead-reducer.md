---
name: "Backhaul / Deadhead Reducer"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/week"
version: 1.1
last_eval_score: 8.9
---

# 🔁 Backhaul / Deadhead Reducer

## Purpose

Turn a week's load list, committed outbound lanes, and available equipment into a ranked set of backhaul-pairing and triangulation opportunities — each with estimated empty miles saved, revenue added, driver-hour feasibility, and ready-to-use sourcing actions (post to load board, call target broker, pull from committed shipper pool).

## When to Use

Use this skill during the Friday / Monday planning huddle, after the routing-guide rerate, whenever a new lane is added with an unfavorable headhaul ratio, when fuel prices spike and deadhead dollars become painful, or any time the operations team notices a persistent empty-mile pattern in a region (e.g., trucks consistently running 200+ empty miles out of a Kansas City drop). It is equally useful for a small carrier with 15 trucks and for a broker trying to place a partner's return leg profitably.

## Required Input

Provide the following:

1. **Outbound load list** — For each committed load this week: pickup city/state, delivery city/state, equipment type, delivery day, estimated unload complete time, and driver/truck assignment if set
2. **Equipment and HOS snapshot** — Available driver hours at the drop city for each truck (11/14/70 remaining), home-time or reset windows, equipment restrictions (reefer, flatbed, hazmat endorsement), and any domicile anchor each truck must return to by a given date
3. **Backhaul sources** — Preferred load boards (DAT, Truckstop, 123Loadboard), broker partners for the destination region, committed shipper backhaul pool (shippers with a return-lane agreement), and private shipper contacts
4. **Triangulation constraints** — Maximum acceptable deadhead miles per leg, minimum target RPM for a backhaul, and any lane blacklist (regions the fleet avoids)
5. **Market context (optional)** — Recent DAT RateView or Greenscreens benchmarks on the candidate backhaul lanes, load-to-truck ratios in the drop region

## Instructions

You are a dispatch planner focused on reducing empty miles without breaking HOS or committed delivery windows. Your job is to take the outbound picture and produce a prioritized backhaul plan the dispatcher can execute the same day.

**Before you start:**

- Load `config.yml` for default deadhead tolerance, minimum backhaul RPM, and preferred sources
- Reference `knowledge-base/terminology/` for correct terms (deadhead, backhaul, triangulation, headhaul, RPM, HOS 11/14/70, domicile, tender)
- Note whether the fleet operates regional, OTR, or dedicated — the triangulation logic differs

**Process:**

1. **Group drops by region and day** — Cluster the outbound list into drop regions (3-digit ZIP or commercial market) and by delivery day so pairs of trucks finishing in the same area can share backhaul opportunities
2. **Identify the deadhead exposure** — For each drop, estimate empty miles to the next likely origin (domicile, next committed pickup, or nearest known freight source). Rank drops by deadhead dollars at risk (empty miles × estimated cost per mile from `config.yml`)
3. **Score backhaul candidates** — For each high-exposure drop, propose 2–3 candidate backhaul lanes using load-board, broker partner, and committed-pool data. Score each candidate on:
   - Deadhead-in miles (drop → pickup)
   - Pickup window feasibility given HOS and unload finish time
   - Estimated all-in RPM vs. minimum threshold
   - Destination usefulness (does it set up the next committed move or land at domicile?)
   - Source reliability (committed shipper > preferred broker > spot board)
4. **Build triangulation plans where they pay** — When a single backhaul does not get the truck home or to the next committed move, propose a two-stop triangulation (A → B → C → home) only if total empty miles drop, both legs meet the minimum RPM, and HOS holds
5. **Flag feasibility killers** — Call out anything that would void the plan: driver out of hours, equipment mismatch, reset required before the candidate pickup, weather or port congestion affecting the lane
6. **Recommend and assign actions** — For the top 5–8 opportunities, produce a one-line action the dispatcher can execute today: post to DAT with specific rate floor, call Broker X about lane Y, pull committed-pool return from Shipper Z, or hold for a better candidate tomorrow
7. **Summarize the weekly impact** — Roll up estimated empty miles saved, backhaul revenue added, and net margin improvement across the full plan. Break out which trucks gain the most

**Output requirements:**

- A short executive summary: total empty-mile exposure this week, proposed savings, number of actions
- A ranked opportunity table: truck, drop, candidate backhaul, deadhead-in, RPM, estimated margin, action owner
- Triangulation plans shown as labeled legs (Leg 1 / Leg 2 / return) with HOS and rate math
- Feasibility flags listed separately so they are not lost in the table
- The one-line action assignment per opportunity is in imperative voice ("Post DAT, floor $2.15/mi, 0500 MT pickup Wed") so the dispatcher can work from it directly
- An internal-notes block documenting assumptions (fuel cost used, benchmark source, HOS snapshot timestamp)
- Saved to `outputs/` if the user confirms

## Reference Example

**Input (week's load list + equipment/HOS + sources):**

> Regional dry-van carrier, 15 trucks, Southeast domicile Charlotte NC (CLT). Friday planning huddle for next week. Four trucks finish committed outbound by Wed with no return assigned:
> - **Truck 12 (driver L. Suarez)** — delivers Halverson Manufacturing load CLT → Atlanta GA 30303, unload complete Wed ~1400 ET. HOS at drop: 6:30 drive / 8:00 window / 41 hrs of 70 remaining. Must be home CLT by Fri night (weekend home-time).
> - **Truck 7 (driver M. Reyes)** — delivers CLT → Savannah GA 31401, unload complete Wed ~1100 ET. HOS: 8:00 drive / 10:30 window / 58/70. No hard domicile return until following Mon.
> - **Truck 19 (driver D. Tovar)** — delivers CLT → Nashville TN 37210, unload complete Wed ~1600 CT. HOS: 4:00 drive / 5:00 window / 22/70 — **tight**. Home CLT by Fri.
> - **Truck 3 (driver J. Park)** — delivers CLT → Greenville SC 29601, unload complete Wed ~1000 ET. HOS: 9:30 drive / 11:00 window / 63/70. Flexible.
>
> Sources: DAT + Truckstop; broker partner "Eastbound Logistics" covers GA/TN backhauls; committed-pool shipper "Piedmont Paper" tenders ATL→CLT returns at $2.05/mi. Config: deadhead tolerance 150 mi/leg, min backhaul RPM $1.90 all-in, cost/mi $1.42, lane blacklist = none. Market: DAT ATL→Southeast load-to-truck 4.1, Nashville outbound thin (1.8).

**Backhaul Plan — Week of 2026-06-22 (4 unassigned returns)**

```
Domicile: Charlotte NC (CLT)   Cost/mi: $1.42   Min backhaul RPM: $1.90 all-in   DH tol: 150 mi/leg
═══════════════════════════════════════════════════════════════════════════════════════
Truck Driver    Drop          Naked DH home   $ at risk   Recommended backhaul          RPM
─────────────────────────────────────────────────────────────────────────────────────────
 12   Suarez    ATL 30303     244 mi (→CLT)   $346 empty  Piedmont Paper ATL→CLT (pool) $2.05
  7   Reyes     SAV 31401     249 mi (→CLT)   $354 empty  Triangulate SAV→Charleston→CLT $1.98
 19   Tovar     BNA 37210     408 mi (→CLT)   $579 empty  HOLD — out of hours (see flag) —
  3   Park      GSP 29601      98 mi (→CLT)   $139 empty  Run empty (under DH tol, low $) —
═══════════════════════════════════════════════════════════════════════════════════════
```

**Executive summary.** Four open returns, **$1,418 total naked-deadhead exposure**. Plan converts two to revenue, accepts one short empty as cheapest option, and holds one truck for a reset. **Net: ~$1,490 backhaul revenue added, ~493 empty miles eliminated, 3 actions to work today.**

**Ranked opportunities**

1. **Truck 12 (Suarez), ATL → CLT — committed-pool return, $2.05/mi.** Pull the Piedmont Paper ATL→CLT return from the committed pool. 244 loaded mi, ~3:50 drive — fits Suarez's 6:30 drive / 8:00 window with room. Lands him home CLT Wed night, well inside Fri home-time. All-in **$2.05/mi = ~$500 revenue** vs. $346 of empty. Source reliability: committed pool (highest). **Action owner: dispatch.**
2. **Truck 7 (Reyes), SAV → CLT via Charleston — triangulation.** No single SAV→CLT backhaul clears RPM on the board, but a two-stop pays: **Leg 1** SAV→Charleston SC 29403 (108 mi loaded, DAT board load $2.20/mi, pickup Wed 1600 feasible), **Leg 2** Charleston→CLT (207 mi, Eastbound Logistics partner load $1.85/mi or committed if available). Blended **~$1.98/mi all-in across 315 loaded mi ≈ $990 revenue**, deadhead between legs ~12 mi. HOS holds (58/70, no reset needed; Mon domicile gives slack). **Action owner: dispatch + broker desk.**
3. **Truck 3 (Park), GSP → CLT — run empty.** Only 98 empty mi, under the 150-mi tolerance and $139 exposure. The thin GSP outbound board (no qualifying backhaul ≥ $1.90 within tolerance) means sourcing cost exceeds the empty cost. **Run empty; re-check the board Tue PM for a same-day add.**

**Triangulation detail — Truck 7**

```
Leg 1:  Savannah GA 31401 → Charleston SC 29403   108 mi   $2.20/mi   pickup Wed 1600
Leg 2:  Charleston SC 29403 → Charlotte NC 28202   207 mi   $1.85/mi   pickup Thu 0700
DH between legs: ~12 mi (Charleston drop → Charleston pickup)
Blended all-in: (108×2.20 + 207×1.85) / 315 = $1.98/mi   Revenue ≈ $621 + $383 = $1,004
HOS: Wed 4.0 drive used / 6.5 remaining; 10-hr reset overnight Wed; Thu fresh clock. ✓
```

**Feasibility flags (do not lose in the table)**

- **Truck 19 (Tovar), BNA — OUT OF HOURS.** 4:00 drive / 5:00 window remaining at a 1600 CT finish, and only 22/70 on the cycle. BNA→CLT is 408 mi (~6:30) — **cannot be run loaded or empty today, and a backhaul would require a 34-hr restart before pickup.** Recommendation: 10-hr reset BNA Wed night, then either (a) a Thu BNA→Southeast board load if one clears $1.90 (Nashville outbound is thin at 1.8 L:T — unlikely), or (b) deadhead home Thu after restart and accept the $579. **Hold the backhaul decision to Tue PM** when Thu BNA boards populate; do not commit a return that the restart can't support.
- **Truck 7 Leg-2 source is a partner-board load, not committed** — confirm Eastbound Logistics tender before releasing Leg 1, or the truck strands in Charleston. Fallback: Charleston→CLT committed pool if Eastbound can't cover.

**Action assignments (work today)**

- **Truck 12:** Pull Piedmont Paper ATL→CLT from committed pool, confirm Wed 1500 ET pickup, rate $2.05/mi. Owner: dispatch.
- **Truck 7:** Book DAT load SAV→CHS $2.20/mi (Wed 1600 pickup) AND secure Eastbound Logistics CHS→CLT $1.85/mi (Thu 0700) as paired triangulation; do not release Leg 1 until Leg 2 is tendered. Owner: dispatch + broker desk.
- **Truck 19:** Reset BNA Wed night; re-scan Thu BNA outbound boards Tue PM before deciding load vs. empty. Owner: dispatch (Tue PM follow-up).

**Weekly impact**

```
Truck   Empty mi eliminated   Backhaul revenue   Net margin add (@ $1.42 cost/mi)
 12          244                  ~$500              ~$500 rev − $346 empty avoided = +$846 swing
  7          ~237 (vs naked)      ~$1,004            +$1,004 rev on 315 paid mi
  3            0 (empty kept)       $0               cheapest option, $139 accepted
 19          held                 TBD Tue            decision deferred
─────────────────────────────────────────────────────────────────────
Plan total: ~493 empty mi eliminated · ~$1,504 revenue added · 3 actions today, 1 deferred
```

**Internal notes**

- Fuel/cost: $1.42/mi all-in cost from `config.yml`; min backhaul RPM $1.90 all-in; DH tolerance 150 mi/leg.
- HOS snapshot timestamp: Fri planning huddle, reflects projected Wed-drop hours — **re-validate Tue PM** against actual ELD before committing Truck 19.
- Benchmark source: DAT RateView L:T ratios (ATL 4.1 favorable, BNA 1.8 thin) drove the hold-vs-source call on Truck 19.
- Truck 3's empty run is a deliberate accepted cost — sourcing a sub-150-mi backhaul on a thin board costs more in dwell than the $139 saved.

---

*Synthetic example — the 15-truck carrier, the four trucks/drivers (L. Suarez / M. Reyes / D. Tovar / J. Park), Halverson Manufacturing, "Piedmont Paper," and "Eastbound Logistics" are illustrative and continuous with the synthetic operator world used across the repo's examples. DAT / Truckstop are real load boards and HOS 11/14/70 rules are real; the quoted lane rates, L:T ratios, and HOS snapshots are synthetic planning values, not from a real tender.*
