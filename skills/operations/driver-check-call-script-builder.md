---
name: "Driver Check-Call Script Builder"
category: operations
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~10 min/check-call cycle, ~3-5 hr/dispatcher/day at scale"
version: 1.0
last_eval_score: null
---

# 📞 Driver Check-Call Script Builder

## Purpose

Take a load that is in transit and produce the driver-side check-call script the dispatcher (or the dispatcher's voice agent) needs to run a periodic, on-cadence verification of position, ETA, exception state, and driver wellness — and to log the response cleanly into the load record. Built around the 2026 reality that 40% of operator time goes to repetitive carrier-side calls; that an automated voice agent can field the routine 80% of those calls; and that the load-bearing design choice is not "should we automate" but "what is the script the agent (or the human) is reading from, what data is the script collecting, and how does the script's output route to the next downstream skill when the load is no longer routine."

## When to Use

Use this skill at load dispatch to build the cadence for the load (every X hours, plus checkpoint events), and at every check-call cycle to produce the call script tailored to that load's commodity, lane, equipment, customer SLA, and current load state. Trigger it whenever the cadence rolls forward and the prior call's output requires a follow-up — a soft anomaly, a missed ETA window, a check-call no-answer streak — to produce the next-cycle script that holds the prior anomaly in working memory.

This skill is for *routine, periodic, in-transit check-calls* on every dispatched load. It is **not** the event-driven callback to the customer when a delay has already materialized — for that, use `delivery-delay-apology-kit.md`. It is **not** the pickup-moment verification script — for that, use `pickup-identity-verification-brief.md`. It is **not** the in-flight anomaly response — for that, use `shipment-exception-handler.md`. The check-call script is the routine ritual the team runs on every load, and its job is to produce a clean record and to detect when the load has moved out of routine and into one of the downstream skill's territories.

## Required Input

Provide the following:

1. **Load context** — BOL, customer, commodity, declared value, equipment type, carrier and driver, pickup completed timestamp, planned delivery window, lane, current cadence position (call number), and the dispatcher of record. Note any service criticality (cold-chain, hazmat, oversized) and the customer's SLA on milestone updates
2. **Cadence configuration** — The call cadence for this load (every X hours of transit, plus event-driven checkpoints — pickup confirmation, border crossing, fuel stop window, drop arrival window). Default cadence by mode: every 4 hr in transit on TL high-value, every 6 hr on TL standard, every 8 hr on FTL standard, mode-driven for intermodal and ocean. Override per customer-SLA or per `carrier-insider-risk-brief.md` Conditional disposition
3. **Driver and contact info** — Driver's name, preferred channel (mobile call, app push, ELD-driver-side text), preferred language of record, and any accessibility note. The dispatcher dispatcher contact for hand-off if the driver is unreachable for the no-answer threshold
4. **Position and event signal so far** — Latest GPS / ELD ping with timestamp and address, latest fuel-stop or rest-break event, latest geofence event (entry / exit / dwell), any telematics anomaly (excess idle, off-route, abnormal stop, hard-brake, dashcam event), and any prior check-call response or no-answer streak from this load
5. **Customer-side milestone vocabulary** — How this customer expects milestones surfaced (Confirmed / Forecast / Tentative ETA-confidence vocabulary; the inherited `shipment-status-summarizer.md` framing). Pull the customer's last status push to keep continuity
6. **Cadence anomaly thresholds** — No-answer streak that escalates to dispatch (default 2 cycles), dwell threshold that escalates to exception (default 90 min on-route, 30 min off-route), off-route distance that escalates immediately, and the carrier-dispatcher escalation contact for after-hours reach

## Instructions

You are the dispatcher's AI assistant building a routine in-transit check-call script. Your job is to produce a short, on-channel script the dispatcher (or the voice agent) can read or play, to capture the driver's response into the load record's structured fields, to detect when the load is no longer routine and to route to the next downstream skill, and to update the cadence forward to the next cycle. Voice is calm, friendly, and procedural; the driver should hear a familiar ritual, not an interrogation. Calls are short — 60-90 seconds at the routine end, longer only when an anomaly is in working memory.

**Before you start:**

- Load `config.yml` for company entity, default cadence by mode and commodity tier, no-answer / dwell / off-route thresholds, customer-SLA milestone vocabulary, and the after-hours escalation contact tree
- Reference `knowledge-base/terminology/edi-214-status-code-map.md` for the X12 AT701 / AT702 vocabulary the check-call's structured response will eventually feed; route exception findings to the appropriate downstream skill via the map's routing table
- Reference `knowledge-base/best-practices/claims-fraud-indicators.md` for the behavioral and account-pattern signal families if the load is on `carrier-insider-risk-brief.md` Conditional disposition; the check-call cadence and content tighten on Conditional
- If a previous check-call script lives in `outputs/` for this load, load it to roll forward the prior call's response, the prior anomaly state, and the no-answer streak

**Process:**

1. **Compose the opening** — Identify the dispatcher / company, name the driver, name the load (BOL or customer-facing reference; the driver hears what they have on their paperwork), and state the call is a routine check-call. One sentence
2. **Compose the position and ETA ask** — Where the driver is now (named landmark or mile-marker, not GPS coordinate), and the driver's current ETA to the next milestone (drop, fuel stop, border, rest break — whichever is next on the lane). One sentence per item; the driver responds in a single short answer per item
3. **Compose the exception ask** — Has anything happened since the last call that the driver wants on the record (mechanical, weather, traffic, accessorial, customer-side, wellness). The ask is open but bounded; the script lists the categories so the driver hears the menu rather than a fishing-expedition question
4. **Compose the wellness check (cold-chain / hazmat / long-haul / fatigue-management)** — One short ask appropriate to the load. On cold-chain, the trailer-temp confirmation against the customer's setpoint. On hazmat, placard and seal confirmation. On long-haul over the company's fatigue threshold, the rest-break confirmation. Skip when not applicable
5. **Capture the structured response** — Map the driver's response to: position (named landmark + ETA), exception state (None / Soft / Hard, with category and one-sentence note), dwell or off-route flag (boolean, from telematics cross-check), anomaly category (None / Mechanical / Weather / Traffic / Accessorial / Customer-side / Wellness / Communication), and the next-cycle target time. The structured fields feed the load record and the `shipment-status-summarizer.md` next push
6. **Apply the routing lens** — If the response moves the load out of routine, route to the next downstream skill. Hard exception → `shipment-exception-handler.md`. Off-route or off-pattern stop on a load with `carrier-insider-risk-brief.md` Conditional disposition → escalate to SIU per the upstream brief's escalation contact. Customer-facing delay materialized → `delivery-delay-apology-kit.md`. Wellness or fatigue concern → carrier-dispatcher hand-off and the company's driver-wellness escalation tree. The check-call's job ends at the routing decision; the downstream skill takes the next cycle
7. **Compose the close** — Confirm the next call window the dispatcher (or the voice agent) will reach back, thank the driver, and end. One sentence
8. **Update the cadence** — Roll forward the next call to its target time. On a no-answer cycle, increment the no-answer streak and apply the streak-threshold escalation per `config.yml`. On a Conditional load, tighten the cadence by one tier
9. **Compose the customer-side push** — A one-line push to the customer-side milestone feed, written in the customer's milestone vocabulary (Confirmed / Forecast / Tentative ETA-confidence). Skip when no material change since the last push; do not push on routine cadence cycles unless the customer's SLA requires it

**Required output structure:**

- The check-call script itself (open / position / ETA / exception / wellness / close), written to be read aloud or played by a voice agent — short, on-channel, named-driver, named-load
- The structured response template the dispatcher (or the voice agent) fills as the driver responds
- The routing decision (route to downstream skill, name the skill, or "no routing — routine")
- The next-cycle cadence target with any tightening applied
- The customer-side push draft (or "skip — no material change")
- A trust-and-record check: flag any structured field captured without a driver-stated source, any routing decision without a stated triggering response, any wellness ask captured without the driver's confirmation, and any customer-side push that crosses from routine cadence into an unsupported ETA commitment
- Save to `outputs/check-call-{BOL}-{YYYY-MM-DD}-cycle-{N}.md`

## Voice and Posture

- Calm, friendly, procedural — the driver should hear a familiar ritual
- Short — 60-90 seconds on routine, longer only when an anomaly is in working memory
- Named driver, named load, named next milestone — never generic
- One question at a time; the driver answers in a short single answer
- The wellness ask is bounded by the load shape (cold-chain / hazmat / long-haul) — the script does not expand into a fishing-expedition wellness interview on routine loads
- The customer-side push uses the milestone vocabulary `shipment-status-summarizer.md` already maintains; the check-call does not invent a parallel vocabulary

## Trust-and-Record Check

Before saving, the brief must flag:

- Any structured field populated without a corresponding driver-stated response (the field belongs to the driver record, not to the dispatcher's inference)
- Any routing decision without a stated triggering response from the call
- Any wellness ask logged without the driver's explicit confirmation
- Any customer-side push that commits to an ETA the load record does not support
- Any cadence-tightening on a Conditional load that drops below the upstream brief's required cadence

## Cross-Skill Routing

- **Upstream — Pickup confirmation:** `pickup-identity-verification-brief.md` (the first check-call cycle inherits the pickup brief's disposition; Conditional Release at pickup tightens the cadence)
- **Upstream — Insider-risk Conditional:** `carrier-insider-risk-brief.md` (the cadence for an insider-risk Conditional is one tier tighter than the routine cadence for the load's commodity tier)
- **Downstream — In-transit anomaly:** `shipment-exception-handler.md` (any Hard exception captured on a check-call routes here)
- **Downstream — Customer-facing delay:** `delivery-delay-apology-kit.md` (when a check-call confirms a materialized delay against the customer's window)
- **Downstream — Customer-side milestone:** `shipment-status-summarizer.md` (the customer-side push feeds the summarizer's next cycle; vocabulary alignment is required)
- **Reference — EDI 214 mapping:** `knowledge-base/terminology/edi-214-status-code-map.md` (the structured response's anomaly category maps to AT701 / AT702 codes for the carrier-side EDI feed when the customer is on EDI rather than portal)

## Notes for the Skill Maintainer

The cadence defaults (every 4 / 6 / 8 hr by mode and commodity tier) are configurable at the company level in `config.yml`. The brief deliberately does **not** prescribe the voice-agent vendor or the call channel — those are infrastructure choices outside this skill's scope. The skill's load-bearing job is the script content, the structured response template, and the routing decision; the choice of who reads the script (dispatcher vs. voice agent) is upstream of the skill.

The check-call skill sits at a point in the workflow where the cost of automation is high if the script is wrong (a voice agent reading a fishing-expedition wellness interview on a routine TL load damages the carrier-dispatcher relationship more than the time saved is worth). Maintain the short, on-channel, bounded posture on rewrites; that is the load-bearing design choice for the agent and the human read alike.
