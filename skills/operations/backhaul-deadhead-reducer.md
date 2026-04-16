---
name: "Backhaul / Deadhead Reducer"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/week"
version: 1.0
last_eval_score: null
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

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
