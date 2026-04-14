---
name: "Dock Scheduling & Detention Prevention Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/brief"
version: 1.0
last_eval_score: null
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

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
