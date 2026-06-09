---
name: "Last-Mile Pre-Positioning Advisor"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/cycle"
version: 1.1
last_eval_score: 8.8
---

# 📍 Last-Mile Pre-Positioning Advisor

## Purpose

Turn a short-horizon demand forecast, weather outlook, and local-event signal set into a concrete last-mile pre-positioning plan: which depots or micro-fulfillment nodes need more inventory, more drivers, or more vehicles by which day, how many of each, and what the dollar downside is if the move is not made. Produces the one-page plan the depot manager acts on plus the field-team message that tells drivers what changes.

## When to Use

Use this skill for the 7–14 day last-mile planning cycle, ahead of a forecasted weather event (typhoon, hurricane, winter storm, heat dome) that will compress a delivery window, in the week before a named local event with predictable volume impact (stadium event, convention, regional holiday, big-box promo drop), during seasonal ramps (peak, Prime-day tier events, produce season, back-to-school) where volume is known to move between depots, or any time a national forecast lands but the regional decision of "which depot stocks what" has not been made yet.

## Required Input

Provide the following:

1. **Forecast by depot** — Volume (parcels, stops, or route-hours) per depot or micro-fulfillment node for the next 7–14 days; include the prior forecast so drift is visible
2. **Current position snapshot** — On-hand inventory at each depot (if applicable), available driver headcount, owned/contracted vehicle count by class (van, cargo van, step van, EV), and current capacity utilization
3. **External signals**
   - Weather outlook for each service territory (3–10 day): precipitation, wind, extreme heat/cold, named-storm tracks with landfall windows
   - Local events calendar (stadium, convention, festival, school-calendar anomalies, street closures)
   - Marketplace promotions and customer-specific volume commitments that are already booked
4. **Capacity levers available** — What can actually be moved: pallet transfers between depots, surge labor pool, rental-vehicle agreements, temporary parking for staged vehicles, EV charging capacity, contractor / gig network availability
5. **Planning objective** — Service-level protection (maintain promise date during storm), cost minimization (avoid same-day repositioning penalties), or growth launch (new depot going live in N days)

## Instructions

You are a last-mile operations planner translating a forecast and a set of external signals into a pre-positioning plan that depot managers, dispatch, and the field team will execute. Your job is to quantify the risk, propose moves that sit inside the available levers, and make the trade-offs visible so the regional manager can approve in minutes, not hours.

**Before you start:**

- Load `config.yml` for default service-level targets, repositioning cost thresholds, weather-severity classes, and lead-time assumptions per move type
- Reference `knowledge-base/terminology/` for correct terms (stops-per-hour, density, drop count, EDD, promise date, SLA, contractor network, gig tier, micro-fulfillment, dispatch block)
- Confirm that the forecast has been produced or sanity-checked by the Demand Forecasting Summary Brief — if MAPE on the relevant depots is >30%, flag the plan as working off a low-confidence base

**Process:**

1. **Validate the forecast at depot grain** — Check the per-depot forecast for obvious anomalies (a 4× jump on a small depot with no known driver, a depot missing from the file, negative values). Note depots where the forecast is materially above historical peak and depots where it is below historical floor
2. **Layer external signals onto the forecast** — For each depot: (a) overlay the weather outlook and flag days where precipitation or wind will degrade stops-per-hour (apply a productivity haircut — e.g., 15–35% — scaled to severity), (b) overlay local events and flag days where either demand spikes (event attendee deliveries, marketplace pre-game orders) or street access degrades (closures, parade routes), (c) flag where a named-storm landfall compresses a service window into fewer available hours
3. **Compute the gap by depot by day** — For each depot × day, compute expected demand (stops or route-hours) minus effective capacity (drivers × productivity × hours available × vehicle count). Report the gap in stops, route-hours, and dollars at risk (failed-delivery cost + SLA-miss penalty)
4. **Rank the top pre-positioning moves** — Produce the 5–10 moves that close the largest portion of the gap per dollar of cost, each described as a single line: "Transfer 1,200 parcels from Depot A to Depot B by Tue 6am," "Pre-stage 4 EV step-vans at Depot C from Depot D by Mon 10pm," "Shift 6 contractor routes from Depot E to Depot F for Wed–Fri," "Open Saturday shift at Depot G." Show the expected stops covered and the cost per move
5. **Build the combined plan** — Select the moves that together close the gap without blowing the budget; show residual risk where the plan is deliberately not closing a gap (accepting SLA miss on a low-margin lane, for example). Include a contingency move for each major risk: if the storm shifts east, what changes
6. **Draft the depot-manager sheet and the driver-facing note** — The depot-manager sheet is a one-pager with the moves, owners, deadlines, and the residual risk. The driver-facing note is 3–6 short lines in plain language telling drivers what's different — routes being rebalanced, a new start time, a weather advisory, a staged vehicle — without jargon
7. **Flag the decisions that need regional-manager approval** — Any move that exceeds the per-move budget threshold in `config.yml`, any move that pulls from a neighboring region's pool, any move that opens a premium-pay shift, or any move that accepts a visible SLA miss

**Output requirements:**

- A one-paragraph executive summary at the top: horizon, top depot at risk, top recommended move, dollars at risk if no action taken
- A small table of depot × day gaps with effective capacity, expected demand, and gap in stops and dollars
- A ranked pre-positioning action list — each with owner, deadline, cost, stops covered, and confidence
- A weather/event overlay section naming the specific signal driving each move (so the plan is legible when the signal changes)
- A contingency block for each primary risk (storm track shift, event cancellation, contractor-pool shortfall)
- A depot-manager one-pager and a short driver-facing field note, each in its own section
- An internal-notes block with forecast timestamp, signal sources cited, and any productivity-haircut assumptions applied
- A "needs approval" list isolating the moves above the discretionary threshold
- Saved to `outputs/` if the user confirms

## Reference Example

**Input (7-day cycle + storm signal):**

> Carolinas last-mile network, 7-day horizon 2026-06-08 → 06-14, objective **service-level protection**. Four depots, forecast in stops/day (prior forecast in parens):
> - **CHS-4 Charleston SC** — Mon 920, Tue 880, Wed 1,180 (↑ from 760), Thu 1,050, Fri 900. Mgr D. Tovar.
> - **RDU-1 Raleigh NC** — Wed 1,400, Thu 1,620 (↑ from 1,180), Fri 1,500.
> - **CLT-2 Charlotte NC** — steady ~1,300/day.
> - **GSO-3 Greensboro NC** — steady ~640/day.
>
> Current position: CHS-4 22 drivers / 24 vans; RDU-1 34 drivers / 38 vans; CLT-2 30 drivers / 33 vans; GSO-3 14 drivers / 16 vans. Normal productivity ~18 stops/driver-day.
> External signals: **Tropical Storm Bonnie** — NHC track has landfall near Charleston **Wed 06-11 ~1400 ET**, tracking inland to the Raleigh corridor by **Thu 06-12**. Bands of 2–4" rain + 35–45 mph gusts. RDU also has a **3-day convention (06-12→06-14, ~14k attendees)** lifting residential + locker volume. Levers: pallet/parcel transfer between depots (overnight linehaul), surge gig pool (RDU + CLT metros), 6 rental step-vans available regionally, Saturday shift authorizable. Forecast MAPE last cycle: CHS-4 19%, RDU-1 14% (both under the 30% low-confidence line).
>
> Config: SLA target 97% on-time; per-move budget threshold $3,500 (above = regional-manager approval); weather-severity classes (TS landfall = "Severe" → 25–35% productivity haircut); transfer lead time 1 overnight; gig-pool spin-up 24 hr.

**Pre-Positioning Plan — Carolinas, 06-08 → 06-14**

**Executive summary.** Tropical Storm Bonnie compresses the delivery window at **CHS-4 Charleston on Wed 06-11** (landfall ~1400 ET, "Severe" class → 30% productivity haircut) and at **RDU-1 Raleigh on Thu 06-12**, where the storm overlaps a 14k-attendee convention driving a forecast spike to 1,620 stops. **Top risk: RDU-1 Thursday, ~430-stop gap, ~$10.8K at risk.** The recommended package — two overnight transfers, a 24-hr gig spin-up at RDU, and 6 staged rental vans — closes ~85% of the network gap for ~$8.9K; the residual is a deliberate accepted miss on a low-density GSO overflow lane. Approve by **Mon 06-08 1700 ET** so the first transfer moves tonight.

**Depot × day gap (stops; storm/event days only)**

```
Depot  Day        Demand  Eff.Capacity  Gap    $ at risk  Driver
─────────────────────────────────────────────────────────────────────
CHS-4  Wed 06-11   1,180     302*       -878†  storm-shed  TS landfall 1400
CHS-4  Thu 06-12   1,050     396*       -654†  storm-tail  bands clearing
RDU-1  Thu 06-12   1,620   ~1,190‡      -430   $10,750     convention + storm
RDU-1  Fri 06-13   1,500    1,224‡      -276   $ 6,900     storm-tail + event
CHS-4  Wed (recov) deferred volume rolls to Thu/Fri — see note
─────────────────────────────────────────────────────────────────────
* CHS-4 Wed capacity = 22 drv × 18 × 0.70 (30% Severe haircut) ≈ 277, +rental ≈ 302;
  much of Wed CHS-4 demand is undeliverable in-window and pre-emptively re-promised
  (EDD push), not forced into the storm — the gap there is managed by re-promise, not capacity.
‡ RDU-1 base = 34 drv × 18 × 0.85 (storm-tail 15% haircut Thu) ≈ 520... [normalized to
  stops incl. multi-stop routes ≈ 1,190 effective]; gap is the convention spike, not weather.
† CHS-4 storm-day gap is shed via EDD re-promise (see overlay), not counted toward the
  move budget — you cannot safely add capacity into a landfall window.
```

**Weather / event overlay (what drives each move)**

- **CHS-4 Wed 06-11 — TS Bonnie landfall (Severe).** Do **not** add capacity into the landfall window. Pre-emptively re-promise Wed non-urgent stops to Thu/Fri (EDD push), prioritize medical/perishable only, pull drivers off road by 1300 ET. Wed volume rolls forward → lifts Thu/Fri CHS-4 load.
- **RDU-1 Thu 06-12 — convention spike + storm tail.** The 1,620 forecast is the binding gap. Storm tail (15% haircut) compounds it. This is where capacity should go.
- **RDU-1 Fri 06-13 — event day 2 + recovery backlog.** Residual 276-stop gap; partly absorbed by Thursday's surge carrying into Friday.

**Ranked pre-positioning moves**

```
#  Move                                              Owner       Deadline       Cost    Stops  Conf
──────────────────────────────────────────────────────────────────────────────────────────────────
1  Spin up 24-hr gig pool at RDU-1 (+12 routes Thu)  J. Park     Mon 06-08 1700 $2,880  ~216  High
2  Transfer 5 staged rental step-vans → RDU-1        K. Mahoney  Tue 06-09 2200 $1,750  ~140  High
3  Overnight parcel transfer CLT-2 → RDU-1 (Thu vol) M. Reyes    Wed 06-10 2300 $1,450  ~120  Med-Hi
4  Authorize RDU-1 Saturday shift (Fri recovery)     J. Park     Thu 06-12 1200 $1,900  ~180  Med
5  Stage 1 rental van + 4 gig routes at CHS-4 (Thu   D. Tovar    Wed 06-11 1800 $  920  ~ 72  Med
   recovery, NOT Wed landfall)
──────────────────────────────────────────────────────────────────────────────────────────────────
   Package total                                                                $8,900  ~728
```

**Combined plan & residual risk.** Moves 1–4 close the RDU-1 Thu/Fri gap (706 of ~706 stops) and Move 5 covers CHS-4 Thursday recovery. **Residual accepted miss:** a ~90-stop low-density GSO-3 overflow lane on Fri is left uncovered — repositioning into it costs ~$2,100 to save <$1,400 of SLA exposure, so it is deliberately not closed; those stops re-promise to Sat at standard cost. Network SLA holds at ~97% on the protected lanes.

**Contingency block**

- **If Bonnie tracks east (weaker CHS landfall):** release Move 5, hold the CHS-4 rental van in reserve, redirect that gig spend to RDU-1 Friday (the convention gap is track-independent).
- **If Bonnie tracks west/stalls (Charlotte impact):** CLT-2 becomes a risk depot — pause Move 3 (do not pull CLT-2 parcels), spin a separate CLT-2 gig pool instead; re-run this plan with CLT-2 as a third storm depot.
- **If convention attendance underperforms:** stand down Move 4 (Saturday shift) — it is the lowest-confidence move and the easiest to cancel late.

**Depot-manager one-pager (RDU-1 — J. Park)**

> RDU-1, Thu–Sat: you're the network's top-risk depot — convention + storm tail, ~430-stop Thursday gap. Inbound help: **+12 gig routes Thu** (confirmed Mon), **+5 rental step-vans Tue 2200**, **CLT-2 parcel transfer landing Wed overnight**, **Saturday shift authorized for Friday recovery**. Owners/deadlines on the move table. Hold the GSO overflow lane — those ~90 Friday stops re-promise to Saturday by design, don't burn surge on them.

**Driver-facing field note (CHS-4 — Wed 06-11)**

> Heads up team — Tropical Storm Bonnie makes landfall near us Wed afternoon (~2pm). **Wednesday routes are trimmed to essential/perishable only** and we're moving everyone off the road by 1pm — safety first, no exceptions. Many Wednesday stops were already pushed to Thursday, so your Thursday routes will run heavier and we've got extra vans staged. Watch dispatch for your updated Wednesday cut-time and Thursday start. Stay safe.

**Internal notes.** Forecast timestamp 2026-06-08; MAPE CHS-4 19% / RDU-1 14% (both < 30% confidence line, per Demand Forecasting Summary Brief cross-check). Productivity haircuts: CHS-4 Wed 30% (Severe/landfall), RDU-1 Thu 15% (storm tail) — per config weather-severity classes. Signal sources: NHC advisory (TS Bonnie track), RDU convention calendar.

**Needs regional-manager approval**

- **Move 4 — RDU-1 Saturday shift ($1,900 premium-pay):** under the $3,500 per-move threshold but opens a premium shift → flag for K. Mahoney (regional) sign-off.
- **None of the moves pull from a neighboring region's pool.** All gig/rental capacity is in-region. No cross-region escalation required.

---

*Synthetic example — the four Carolinas depots (CHS-4 / RDU-1 / CLT-2 / GSO-3), the stop forecasts, driver/van counts, the D. Tovar / J. Park / K. Mahoney / M. Reyes owners, and "Tropical Storm Bonnie" are illustrative. NHC and the convention are real signal types; this specific storm, track, and event are fictional. Productivity-haircut percentages and dollar-at-risk figures are synthetic planning assumptions, not measured values.*
