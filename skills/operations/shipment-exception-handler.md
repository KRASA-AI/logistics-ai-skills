---
name: "Shipment Exception Handler"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/exception"
version: 1.1
last_eval_score: 8.8
---

# 🚨 Shipment Exception Handler

## Purpose

Triage shipment exceptions (delays, damages, refused deliveries, weather holds, customs holds) and produce a structured response plan with root-cause analysis, corrective actions, and ready-to-send stakeholder communications.

## When to Use

Use this skill when a shipment exception or disruption alert arrives and you need to quickly assess impact, decide on corrective actions, and communicate with all affected parties (shipper, consignee, carrier, internal ops). It turns raw exception data into an actionable response plan in minutes instead of requiring multiple phone calls and ad-hoc emails.

## Required Input

Provide the following:

1. **Exception details** — The alert, notification, or description of the disruption (e.g., delay notification from carrier, damage report from receiver, customs hold notice)
2. **Shipment context** — PRO number, origin, destination, commodity, value, delivery commitment, customer priority tier
3. **Constraints** — Deadlines, customer SLAs, available rerouting options, budget limits for expedited recovery

## Instructions

You are an experienced logistics exception-management specialist's AI assistant. Your job is to triage shipment exceptions and produce a structured response plan with stakeholder communications.

**Before you start:**
- Load `config.yml` from the repo root for company details, escalation contacts, and SLA tiers
- Reference `knowledge-base/terminology/` for correct industry terms
- Reference `knowledge-base/regulations/` if the exception involves compliance (hazmat, customs, temperature-controlled freight)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Classify the exception** — Categorize by type (delay, damage, shortage, refusal, regulatory hold, force majeure) and severity (low / medium / high / critical)
2. **Assess impact** — Determine downstream effects on delivery commitment, customer SLA, revenue at risk, and any cascading shipments
3. **Identify root cause** — Based on the available information, determine the most likely root cause (carrier failure, weather, documentation error, warehouse error, customs issue)
4. **Recommend corrective actions** — Provide 2–3 ranked options (e.g., reroute via expedited carrier, hold and wait, split shipment) with estimated cost and time trade-offs
5. **Draft stakeholder communications** — Produce ready-to-send messages for each audience:
   - **Customer/consignee** — Professional, empathetic, solution-focused update
   - **Carrier** — Firm, factual escalation or inquiry
   - **Internal ops team** — Concise situation report with recommended next steps
6. **Flag follow-ups** — List any items that need monitoring (e.g., "check carrier update at 1400", "confirm reroute pickup by EOD")

**Output requirements:**
- Structured exception report with severity badge and impact summary at the top
- Clear action items with owners and deadlines
- All communications ready to copy-paste with minimal editing
- Professional formatting appropriate for logistics operations
- Correct industry terminology throughout
- Saved to `outputs/` if the user confirms

## Reference Example

**Input (raw alert + shipment context):**

> Carrier alert 06:42 ET — PRO 884-71203 (Tier-1 customer Halverson Manufacturing, contract MSA-2024-0114). Reefer trailer 32194 reported a compressor fault at the Tallapoosa GA fuel stop overnight; box temp drifted to 58°F by 04:50 ET (load is pharma-adjacent cold-chain medical components, validated 2–8°C / 36–46°F per customer-specified handling profile). Driver swapped trailer at the carrier's Atlanta yard at 05:55 ET, transferred load, departed 06:30 ET. Origin DFW → Destination Charlotte NC. Original delivery commitment: 2026-06-02 0800 ET appointment at Halverson Charlotte plant. New ETA per carrier dispatch: 06-02 1230 ET (4.5 hr late). Load value $187K invoice, $43K product-specific cold-chain damages exposure per master agreement §8.3. Halverson plant ops manager (D. Tovar) flagged that the Charlotte line is on a JIT schedule and a 4.5-hr late arrival pushes their afternoon production run by one shift. Two backup loads of the same SKU are en route from a different supplier, ETA 06-03 1400 ET. Internal: dispatcher J. Park is the carrier-side owner; account manager L. Suarez owns the customer relationship; QA lead M. Reyes owns the temperature-excursion disposition.

**Exception Report**

```
═══════════════════════════════════════════════════════════════
[ CRITICAL ] PRO 884-71203 — Reefer Excursion + 4.5-hr Late
═══════════════════════════════════════════════════════════════
Classification:  Temperature excursion (cold-chain) + delay
                 — compound exception, two separate dispositions
Severity:        CRITICAL (Tier-1 customer + cold-chain product
                 + temp excursion outside validated range +
                 customer JIT impact)
Customer:        Halverson Manufacturing (Tier-1)
Lane:            DFW → CLT
Commit:          06-02 0800 ET   →   Revised: 06-02 1230 ET
Late by:         4.5 hours
Cold-chain:      Box temp 58°F at 04:50 ET (peak observed)
                 — outside 36–46°F validated window
                 — duration above 46°F: ~2h 35m per Geotab log
                 — recovery temp at 06:30 ET departure: 39°F
Revenue at risk: $43K cold-chain damages exposure (§8.3)
                 $187K invoice (full-load value)
                 Customer's downstream: 1-shift production slip
Cascading:       None — backup loads from second supplier
                 ETA 06-03 1400 ET, no other Halverson loads in
                 the next 48 hr window
```

**Root-Cause (preliminary, pending carrier confirmation):**
Mechanical — reefer compressor fault on trailer 32194. Carrier dispatch reports this trailer was last preventive-maintenance'd 2026-04-18 (within the 60-day window). Excursion was detected by Geotab telematics at 01:14 ET; driver did not get alert push until 04:30 ET (alert-routing gap to investigate separately). Trailer swap was executed correctly per cold-chain SOP — load was transferred under continuous reefer power within the 12-minute window the SOP allows. Re-cool to 39°F achieved before departure.

**Corrective Actions — Ranked**

1. **Hold current routing, accept the 4.5-hr late ETA, get the temperature-disposition decision from Halverson QA before the load arrives.** Cost: $0 incremental freight. Time: ETA 06-02 1230 ET (locked). Risk: $43K cold-chain exposure if Halverson QA rejects the load; mitigated by Geotab log showing excursion duration (~2h 35m) and recovery within recovery-curve tolerance. **Recommended** — the load is moving, the temperature-disposition is the gating decision, and pulling resources to expedite doesn't change the QA gate.

2. **Expedite the remaining DFW→CLT leg via team-driver swap.** Cost: ~$2,400 incremental linehaul + $400 driver swap coordination. Time: recovers ~2 hr (revised ETA 06-02 1030 ET). Risk: still misses the 0800 appointment by 2.5 hr; QA gate unchanged. **Not recommended** — expediting doesn't move the binding constraint (QA disposition + appointment window).

3. **Reposition one of the backup-supplier loads to land at Halverson early via expedited carrier and treat PRO 884-71203 as a deferred-disposition load.** Cost: ~$4,800 + accessorial. Time: backup arrival potentially 06-02 PM. Risk: customer optics — signals that we expect the original load to fail QA. **Not recommended unless** Halverson QA rejects on the disposition review.

**Stakeholder Communications — Ready to Send**

*Customer (D. Tovar, Halverson Charlotte plant ops manager) — drafted in customer-facing voice from `config.voice`:*

> Subject: PRO 884-71203 — Temperature excursion overnight, revised ETA 06-02 1230 ET, QA disposition needed
>
> D. — Reaching out early on a reefer excursion overnight on the DFW→CLT load (PRO 884-71203). Compressor fault on trailer 32194 at our Tallapoosa fuel stop; box temperature drifted to 58°F at peak (04:50 ET) — outside the 36–46°F validated window for approximately 2 hours 35 minutes. Driver executed a trailer swap at our Atlanta yard at 05:55 ET, load transferred under continuous reefer power within SOP, re-cool to 39°F before departure. Geotab telematics log attached for QA review.
>
> Revised ETA: 06-02 1230 ET (4.5 hours past the 0800 appointment). I know that's a one-shift slip against the Charlotte JIT schedule.
>
> Two asks: (1) can your QA team review the excursion log against the cold-chain validation profile and confirm a disposition before the load arrives — that lets us pre-stage a return move if the call is reject. (2) If it helps, L. Suarez can hop on a call with your team at 0900 ET to walk through the timeline. I'll send the appointment from her calendar in the next 10 minutes if that works.

*Carrier (dispatch lead at carrier, contact per `config.escalation_matrix` → Tier-1 cold-chain events) — firm, factual:*

> Subject: PRO 884-71203 — Reefer excursion + alert-routing gap — request for written incident report + trailer 32194 maintenance history
>
> Reefer excursion overnight on PRO 884-71203 (trailer 32194). Two items we need in writing by EOD 06-02: (1) Full incident report including timeline (compressor fault detection → driver alert → trailer swap → re-cool), root-cause assessment, and remediation on trailer 32194 itself. (2) Maintenance history for trailer 32194 covering the last 12 months — we'll need this for the cold-chain damages disposition under master agreement §8.3 and for the QA review on the customer side.
>
> Separately: the Geotab telematics alert fired at 01:14 ET; the driver did not receive a push notification until 04:30 ET. That alert-routing gap is the operational issue we need to close — please confirm whether this was a routing rule (driver was on rest break and notifications were suppressed), a system fault, or a process miss. We'll need this for the post-incident review and any Tier-1 cold-chain event triggers a §8.3 review on our side.

*Internal ops (Slack #ops-exceptions, addressed to J. Park / L. Suarez / M. Reyes):*

> CRITICAL — PRO 884-71203 (Halverson Tier-1, DFW→CLT) — reefer excursion overnight, 4.5-hr late, $43K cold-chain exposure under §8.3. Trailer swap executed correctly, re-cool to 39°F before departure. Geotab log shows ~2h 35m above 46°F (peak 58°F at 04:50 ET).
>
> Owners locked: J. Park (carrier-side), L. Suarez (customer relationship — drafting 09:00 ET pre-arrival call invite), M. Reyes (temp-excursion disposition — pull Geotab log + cold-chain validation profile, prep §8.3 review package).
>
> Decision needed by 1100 ET: Halverson QA disposition (accept / reject on excursion). If reject, we trigger the backup-load reposition (Option 3 in the report). If accept, load arrives 1230 ET and we close the exception with a §8.3 review packet to the customer EOD.
>
> Carrier-side: written incident report + trailer 32194 maintenance history requested EOD 06-02. Alert-routing gap (Geotab 01:14 ET → driver 04:30 ET) is a separate process review — added to the next carrier-performance scorecard review.

**Follow-Up Flags**

- [ ] **By 0900 ET 06-02** — L. Suarez schedule pre-arrival call with D. Tovar (Halverson)
- [ ] **By 1100 ET 06-02** — M. Reyes confirm Halverson QA disposition (accept / reject)
- [ ] **By 1230 ET 06-02** — Confirm arrival at Halverson Charlotte plant
- [ ] **By EOD 06-02** — Carrier deliver written incident report + trailer 32194 maintenance history; M. Reyes prep §8.3 review package
- [ ] **By 06-03 0800 ET** — Close-out exception summary to Halverson account team (L. Suarez owns)
- [ ] **Next carrier-performance scorecard cycle** — Log the Geotab alert-routing gap as a process review item against this carrier

**Cross-skill handoff:** If QA disposition lands as reject or the §8.3 damages claim is opened, route the package to `claims-documentation-builder.md` — this exception has the temperature-excursion classification, the Geotab evidence log, and the carrier-side incident report attached, which is the input set that skill expects.

---

*Synthetic example — Halverson Manufacturing, Tallapoosa GA fuel stop facility, J. Park / L. Suarez / M. Reyes / D. Tovar contacts, PRO 884-71203, trailer 32194, master agreement §8.3 clause, Geotab telematics payload, and the validated 36–46°F cold-chain window are illustrative. Geotab is a real telematics vendor; the alert payload is not from a real incident.*
