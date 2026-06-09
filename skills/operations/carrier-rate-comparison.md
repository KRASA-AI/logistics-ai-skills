---
name: "Carrier Rate Comparison"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/comparison"
version: 2.1
last_eval_score: 8.8
---

# 💲 Carrier Rate Comparison

## Purpose

Normalize quotes from 2–6 carriers onto an apples-to-apples all-in landed-cost basis, show transit-time and service-level deltas alongside the rate math, and produce a ranked recommendation with a one-paragraph rationale — so the shipping manager or CSR can award the load in minutes instead of rebuilding the math in a spreadsheet.

## When to Use

Use this skill when quotes have landed from multiple carriers (LTL via a TMS rater or individual carrier portals, TL via spot email replies, parcel via published tariffs, intermodal via the IMC's response) and a decision needs to be made before a tender deadline. Also use it when a customer has asked for a side-by-side on a quote package, when a routing-guide refresh requires ranking preferred carriers, or when a non-preferred carrier has underquoted by enough to merit a tier-override exception.

This skill produces a recommendation, not a committed tender. The user still owns the final award.

## Required Input

Provide the following:

1. **Shipment basics** — Origin ZIP, destination ZIP, pickup date, required delivery date (or MABD), total weight, total handling-unit count, class(es) if LTL, equipment type (dry van, reefer, flatbed, 53' intermodal, parcel), commodity, declared value, any hazmat flag
2. **Service requirements** — Guaranteed service needed (yes/no + tier), liftgate (pickup/delivery), inside delivery, residential, appointment required, temperature control, protect-from-freeze
3. **Quote detail per carrier** — Carrier name, SCAC, linehaul rate, fuel-surcharge method (flat $ amount, % of linehaul, indexed to DOE with the week used), accessorial breakdown line-itemized ($ and code), minimum charge applied (yes/no), quoted transit business days, quote expiration / rate valid-through date, claims ratio if known, insurance / cargo liability limit, tender acceptance or on-time % on this lane if known, carrier tier from routing guide (primary / secondary / backup / non-preferred)
4. **Internal context** — Customer (if this is a shipper-bill-to), margin floor in $ and %, lane history (recent awards on this lane and their rates), any customer-imposed carrier restriction (approved carrier list, forbidden list)
5. **Decision posture** — Lowest landed cost, best transit, best service record, best margin, or a weighted blend — state the ranking priority up front

## Instructions

You are a freight-procurement analyst's AI assistant. Your job is to eliminate the arithmetic errors and apples-to-oranges gotchas that cause a shipper to pick the "cheapest" quote and then pay 15% more after accessorials and a missed transit window.

**Before you start:**

- Load `config.yml` from the repo root for margin floor, preferred-carrier tier list, approved SCAC list, customer-tier thresholds, fuel-surcharge reference table, and voice
- Reference `knowledge-base/terminology/` for correct terms (linehaul, all-in rate, fuel surcharge, minimum charge, accessorial, transit time, tender acceptance, MABD, COGSA / Carmack limit, claim ratio)
- Reference `knowledge-base/best-practices/` for any routing-guide rules (when to override a primary, when a backup carrier is acceptable, mode-specific service preferences)
- Use the company's communication tone from `config.yml` → `voice` for any customer-facing summary

**Process:**

1. **Normalize each quote to an all-in landed cost** — Show the math per carrier: linehaul + fuel surcharge (computed to $, not %) + each accessorial with code and $ + minimum-charge adjustment if applicable = all-in carrier rate. If a carrier quoted pct-based FSC, compute the $ FSC from the linehaul × percent; if indexed, apply the week's DOE index from config. Never compare linehaul-only rates — always compare all-in
2. **Normalize transit time** — Convert every quoted transit to business days from the requested pickup date to the projected delivery date. Identify which quotes meet the required delivery / MABD and which do not. A quote that misses MABD is disqualified regardless of rate (or explicitly called out as a conditional override)
3. **Identify service-tier mismatches** — Flag quotes that omit requested accessorials (liftgate-delivery not quoted when required), quotes that are standard-service against a guaranteed-service requirement, and quotes that include services the shipment does not need (driver-assist when none required)
4. **Score the non-rate factors** — For each carrier, score the non-rate variables: tender acceptance or on-time % on the lane, claims ratio, tier per routing guide, cargo liability limit vs. declared value. Scores should be comparable across quotes (e.g., a 0–10 scale) and recorded so the ranking is not rate-only
5. **Compute the weighted ranking** — Apply the decision posture from the input. Lowest-cost → rank by all-in rate. Best-value → apply a weighted score (e.g., rate 50% + transit 20% + service 20% + tier 10%). Margin-based → rank by net margin vs. customer sell rate if this is a brokerage quote package. Show the weights used and the final rank
6. **Identify the trade-offs** — For each non-winning carrier, write one line explaining why they lost (e.g., "Saia is $42 cheaper but transit is 5 days vs. 3; misses customer MABD" or "XPO is primary but $118 above next option without a service premium justifying it"). The runner-up should have a defensible story
7. **Flag exceptions and escalations** — Quote that ties (<$20 spread) → recommend the higher-tier carrier. Non-preferred carrier winning on price alone → flag as exception-approval-required, cite the tier-override rule. Margin below floor → stop and flag before the award. Quote expiring within 24 hours → flag time-pressure on the award decision
8. **Draft the award recommendation** — One-paragraph rationale the shipping manager can paste into the tender: "Award load 847221 to [Carrier] at $[all-in rate]. Saves $[delta] vs. next option; meets MABD of [date]; Primary carrier on this lane at [tier]; includes required [accessorials]." Include the expected margin if this is a sell-side quote

**Output requirements:**

- **Header** — Origin → destination, pickup date, MABD, weight, equipment, quote count, ranking priority used
- **Normalized comparison table** — Carrier | SCAC | Tier | Linehaul | Fuel | Accessorials | All-In | Transit (bus days) | On-Time % | Claims % | Non-Rate Score | Weighted Rank
- **Disqualification list** — Any carrier disqualified for missing MABD, missing service tier, expired quote, or margin floor violation, with the reason
- **Recommendation** — Named winner, all-in rate, transit, and the one-paragraph rationale
- **Runner-up** — Named, with the one-line reason they came second (this is the fallback if the winner's rate expires before award)
- **Exceptions / escalations** — Any tier override or margin issue that needs a human sign-off before award
- **Internal notes** — Fuel-index week used, DOE source, weights applied in the non-rate score, margin floor from config
- Math must be reproducible — show each carrier's arithmetic, not just the total
- Never recommend a carrier below the margin floor or missing a hard service requirement; call it out as a gap instead
- Saved to `outputs/` if the user confirms

## Reference Example

**Input (quote package + shipment context):**

> Brokerage quote package, customer Halverson Manufacturing (Tier-1, sell rate locked at $640 all-in). Origin Atlanta GA 30303 → Charlotte NC 28202, dry-van LTL. Pickup 2026-06-10, MABD 2026-06-12. 4,200 lbs, 6 handling units, class 70, packaged industrial components, declared value $48K. Liftgate delivery + delivery appointment required. Decision posture: best-value weighted blend. Four quotes landed:
> - **Saia (SCAC SAIA, primary):** linehaul $312, FSC 32% of linehaul, liftgate-delivery $52 + appointment $30, no min-charge. Transit 2 bus days. On-time 96% / claims 0.4% on lane. Rate valid through 06-13.
> - **Old Dominion (ODFL, primary):** linehaul $358, FSC 29.5% of linehaul, liftgate $48 + appointment $25. Transit 1 bus day. On-time 98.7% / claims 0.2%. Valid through 06-16.
> - **Estes (EXLA, secondary):** linehaul $295, FSC indexed to DOE (06-08 national avg, carrier scale = 34% effective), liftgate $55 + appointment $35. Transit 3 bus days. On-time 91% / claims 0.9%. Valid through 06-12.
> - **AAA Cooper (AACT, non-preferred):** linehaul $268, FSC flat $95, liftgate $50 + appointment $30. Transit 2 bus days. On-time 93% / claims 0.6%. Valid through 06-11.
>
> Config: margin floor 12% / $75 min; cargo liability must cover declared value; tier-override on a non-preferred carrier requires named approval; best-value weights = rate 50% / transit 20% / service 20% / tier 10%.

**Rate Comparison — Halverson LTL, ATL 30303 → CLT 28202**

```
Lane:        Atlanta GA 30303 → Charlotte NC 28202 (dry-van LTL)
Pickup:      2026-06-10    MABD: 2026-06-12 (2 bus-day window)
Freight:     4,200 lbs / 6 HU / class 70 / decl. value $48K
Service:     Liftgate delivery + delivery appointment (both required)
Posture:     Best-value blend (rate .50 / transit .20 / service .20 / tier .10)
Sell rate:   $640 all-in (Tier-1 Halverson)   Margin floor: 12% / $75
═══════════════════════════════════════════════════════════════════════
Carrier  SCAC Tier   Linehaul  Fuel      Access.  All-In   Transit  OT%   Clm%
─────────────────────────────────────────────────────────────────────────────
AAA Coop AACT non-pf  268.00   95.00(flat) 80.00  443.00   2 bd     93.0  0.6
Estes    EXLA second  295.00  100.30(34%) 90.00   485.30   3 bd     91.0  0.9
Saia     SAIA primary 312.00   99.84(32%) 82.00   493.84   2 bd     96.0  0.4
ODFL     ODFL primary 358.00  105.61(29.5%)73.00  536.61   1 bd     98.7  0.2
─────────────────────────────────────────────────────────────────────────────
Fuel math:  Saia 312×.320=99.84 | ODFL 358×.295=105.61 | Estes 295×.340=100.30
            AAA flat $95 (no index) | DOE wk 2026-06-08 national avg applied to Estes
```

**Disqualifications**

- **Estes (EXLA)** — 3-business-day transit projects delivery 2026-06-15, **misses MABD 06-12**. Disqualified regardless of rate. (Also the lowest on-time at 91% and highest claims at 0.9%.)

**Weighted Ranking (3 qualified carriers; Estes DQ'd)**

```
              Rate(.50)  Transit(.20)  Service(.20)  Tier(.10)  WEIGHTED
AAA Cooper      10.0        7.0           6.5          2.0        7.90
Saia             6.7        7.0           8.5         10.0        7.45
ODFL             4.0       10.0          10.0         10.0        7.00
(rate scored low-all-in=best across $443–$536.61 band; service blends OT%+claims)
```

**Recommendation — award to Saia (SAIA) at $493.84 all-in**

> Award the Halverson ATL→CLT LTL load to **Saia at $493.84 all-in**. Saves $42.77 vs. ODFL; meets the 06-12 MABD on a 2-business-day transit; **primary** carrier on this lane at 96% on-time / 0.4% claims; includes the required liftgate-delivery and appointment. Margin at the $640 sell rate is **$146.16 (22.8%)** — clears the 12% / $75 floor. Cargo liability covers the $48K declared value. No tier override required — this is the highest-ranked routing-guide-compliant carrier.

**Margin check (all qualified carriers, $640 sell):** AAA $197.00 / 30.8% ✓ · Saia $146.16 / 22.8% ✓ · ODFL $103.39 / 16.2% ✓ — all clear the floor.

**Runner-up — Old Dominion (ODFL), $536.61 all-in.** Fastest option (1-business-day) and best service record (98.7% / 0.2%), but $42.77 more than Saia with margin compressing to 16.2%. Fall back to ODFL if Saia's rate lapses after 06-13, or promote it if Halverson upgrades this load to next-day.

**Exceptions / escalations**

- **AAA Cooper tops the weighted score (7.90) on rate alone but is non-preferred.** It would save **$50.84 vs. Saia** ($443 vs. $493.84), but it carries the lane's second-lowest on-time (93%) and a non-preferred tier. Per the routing guide, a non-preferred carrier winning on price requires a **named tier-override approval**. **Not auto-recommended** — the $50.84 (7.9% of the all-in) does not clearly justify the override against a Tier-1 customer's MABD-critical load given the on-time gap. If procurement wants the savings, route the override to the approver named in `config.yml` and document AAA's 93% on-time as the accepted trade.
- **AAA's quote expires 06-11** — earliest expiry in the package. If the override path is pursued, the award must clear before EOD 06-11.

**Internal notes**

- Fuel-index week: DOE national average diesel, week of 2026-06-08, applied only to Estes (the one DOE-indexed quote). Saia/ODFL fuel computed from their quoted percent-of-linehaul; AAA used its flat $95.
- Weights from config (`best-value`): rate .50 / transit .20 / service .20 / tier .10. Service sub-score blends on-time % and claims %.
- Margin floor 12% / $75 from `config.yml`; all three qualified carriers clear it.
- Math shown per carrier above; reproducible from linehaul + fuel + line-itemized accessorials.

---

*Synthetic example — Halverson Manufacturing, the four carrier quotes, the lane rates, on-time/claims figures, and the $640 sell rate are illustrative. Saia / Old Dominion / Estes / AAA Cooper and their SCACs are real carriers; the quoted rates and lane stats are not from a real tender. DOE is a real fuel-index source; the applied percentages are synthetic.*
