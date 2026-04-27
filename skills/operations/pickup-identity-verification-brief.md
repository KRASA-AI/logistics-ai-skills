---
name: "Pickup Identity Verification Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/high-risk pickup"
version: 1.0
last_eval_score: null
---

# 🚚 Pickup Identity Verification Brief

## Purpose

Take a load that is about to be picked up — by a carrier the company has already vetted on paper, but where a real driver in a real tractor is now arriving at a real shipper or yard — and produce the pickup-side verification brief the gate, the dispatcher, and the shipper-of-record need to clear the load with confidence or to hold it. Built around the 2026 reality that the dollar exposure on a fictitious pickup is now multiples of the line-haul, that the most common strategic-theft tactic exploits the gap between paper carrier vetting and the moment freight transfers, and that the verification work that closes that gap is a discrete operational ritual the team has to run, not a paperwork box.

## When to Use

Use this skill at the dispatch-to-pickup hand-off on any load that meets one or more of: high-value commodity (electronics, pharma, high-end CPG, copper / metals, household goods over a configurable threshold), high-target lane (corridors flagged in the company's theft-history map), first time loading the company's freight with this carrier or this driver, after-hours / weekend / holiday pickup, last-minute carrier substitution (originally tendered carrier swapped within the last 24 hours), unattended-pickup yard, or any load where the dispatcher's gut-feel concern earned a structured second look. Trigger it whenever a verification anomaly surfaces mid-pickup — wrong tractor, wrong driver, wrong company, wrong arrival window — to produce the hold-or-release decision pack on the spot.

This skill is for the *physical pickup moment*. For the upstream paper vetting of the carrier (authority, insurance, prior-relationship signals, behavioral pattern checks, double-brokering indicators), use `carrier-fraud-screening-brief.md` — this brief assumes that vetting has been run and either passed or passed-with-conditions. For mid-transit anomalies after the pickup is complete, use `shipment-exception-handler.md`. For the post-event teardown if a fictitious pickup or a substitution-fraud incident does occur, use `claims-documentation-builder.md` and route the incident through the company's established loss / SIU playbook — not this skill.

## Required Input

Provide the following:

1. **Load context** — BOL, pickup number, customer, commodity, declared value, equipment type, pickup location (named shipper or yard, address, gate procedure), pickup window, drop location and ETA, and the dispatcher of record. Note any service criticality (cold-chain, hazmat, oversized) that constrains delay
2. **Tendered carrier and driver** — Carrier legal name, MC / DOT, dispatch contact, the tendered driver's name and CDL state, the tendered tractor (unit number, plate, state), the tendered trailer (unit number, plate, state if pulling our trailer or theirs), and the tendered ETA. Note the tender channel (TMS / EDI 204 / load board / direct email)
3. **Carrier vetting status** — Output of `carrier-fraud-screening-brief.md` for this carrier (Proceed / Proceed-With-Conditions / Hold / Block) with the vetting date. If the screening was done more than the company's freshness threshold ago (default 90 days; tighter on theft-target lanes), flag that the brief is operating on stale upstream data
4. **Substitution history (if applicable)** — Whether this carrier was the originally tendered carrier or a substitution. If a substitution, who initiated it, when, the prior carrier, the reason of record, and whether the customer has been notified
5. **Pickup-moment evidence** — At gate arrival: actual driver name on CDL presented, photo on CDL, phone-number used to call dispatch, actual tractor unit number and plate, actual trailer unit number and plate, time of arrival, and any digital-ID artifacts presented (carrier-app QR, encrypted ID token, e-BOL credential). For shippers operating gate cameras, the LPR / image capture timestamp
6. **Any company-side digital-verification feeds** — TMS check-in event, telematics ping showing the tractor at the gate, broker-of-record portal status, and any third-party verification network (carrier-identity feed, plate / ELD telematics match, biometric or selfie-match vendor) the company subscribes to. List which feeds are available for this load and which are not
7. **Hold-and-escalate authority** — Who at the company can authorize a hold beyond what window, who calls the customer if a hold pushes the pickup outside the agreed pickup window, and the named SIU / loss-prevention contact for the suspected-fraud escalation path

## Instructions

You are an operations dispatcher's AI assistant working a pickup-moment verification. Your job is to compare the tendered identity against the arrived identity across the channels available, name every divergence, score the load as Release / Conditional Release / Hold / Escalate, and draft the dispatcher's outbound communications — to the carrier dispatch contact, to the gate, to the customer if a hold pushes the window, and to SIU if a suspected-fraud incident is now in motion. Voice is calm and procedural. The dispatcher does not need an alarm; the dispatcher needs a script.

**Before you start:**

- Load `config.yml` for company entity, theft-target lane map, high-value commodity thresholds, gate procedures by shipper, vetting-freshness windows, and the SIU / loss-prevention escalation contact tree
- Reference `knowledge-base/best-practices/claims-fraud-indicators.md` for the indicator families (image-evidence, account-pattern, document, behavioral) that this brief shares with `claims-documentation-builder.md` and `carrier-fraud-screening-brief.md`. The same lens helps the dispatcher recognize when a pickup-moment divergence is part of a wider pattern rather than a benign mismatch
- Reference `knowledge-base/regulations/` for any state-level CDL display requirements and for the FMCSA carrier-identity context that may govern the company's escalation framing
- If a previous pickup brief lives in `outputs/` for this carrier or this lane, load it to spot recurrence — the same driver under a different MC, the same tractor under a different carrier name, the same plate seen at a recent suspected-substitution event

**Process:**

1. **Reconcile tendered identity to arrived identity** — Build a side-by-side: (a) tendered driver name vs. CDL presented; (b) tendered tractor unit and plate vs. arrived; (c) tendered trailer unit and plate vs. arrived; (d) tendered carrier vs. the carrier whose markings appear on the tractor; (e) tendered ETA vs. arrival time; (f) digital-ID match (where the company runs a verification network) vs. the artifact presented at the gate. Mark every line as Match, Minor Variance, Material Variance, or Mismatch
2. **Apply the substitution lens** — If this carrier was a substitution, the bar is materially higher. The brief should require both the substitution-reason narrative from dispatch and the customer notification confirmation. Last-minute substitutions on theft-target lanes are the single most common shape strategic-theft incidents take in the 2026 coverage; the brief should treat them as Conditional Release at minimum, regardless of the reconciliation outcome
3. **Apply the freshness lens** — If the upstream `carrier-fraud-screening-brief.md` output is older than the company's freshness threshold, that is itself a flag the dispatcher should see at the top of the brief. The brief does not re-run the upstream vetting (it has neither the inputs nor the authority); it surfaces the staleness so the dispatcher can decide whether to rerun the vetting before release
4. **Score the indicator pattern** — Cross-reference the divergences and the carrier history against `claims-fraud-indicators.md`'s four indicator families. Are the divergences isolated (driver-name spelling differs, plate state differs but unit matches, ETA off by 90 minutes — usually benign) or clustered (driver name does not match, tractor does not match, dispatch phone is a different number than the tender, the carrier-of-record signage on the tractor is different — pattern signal)? Score None / Single / Cluster
5. **Land on the disposition** — Release (everything matches; no substitution; vetting fresh; no cluster), Conditional Release (one minor variance with a documented reason, OR a substitution that has cleared customer notification, OR fresh vetting on a high-value load with all checks green), Hold (one or more material variances, OR a Cluster pattern, OR a vetting-freshness gap on a high-value or theft-target load), Escalate (mismatch on driver-or-tractor identity, OR digital-ID artifact failed, OR pattern signal that matches a historical fictitious-pickup shape). The disposition needs to be one word with one short reason — gates and dispatchers act on a single word
6. **Draft the outbound comms** — A short script for the dispatcher to read to the carrier dispatch contact (calmly explaining what is being verified and what the company needs to release the load, never accusing); a short instruction to the gate (release / hold / detain pending dispatcher callback); a short customer note if the hold or escalation pushes the pickup outside the agreed window (factual, no theory of the case); and a short SIU / loss-prevention note if the disposition is Escalate (with the evidence list and the timestamps, no narrative)
7. **Produce the evidence pack** — Whatever artifacts the gate has captured (CDL image, LPR plate image, tractor walkaround photos, driver selfie if a verification vendor was engaged, the digital-ID token presentation, the carrier-app QR scan log) compiled into one block with timestamps and source. This pack is what survives if the load is later disputed or the incident is later investigated — the brief itself is operational, the evidence pack is durable
8. **Schedule the post-pickup loop** — If the disposition was Conditional Release with a follow-up condition (telematics ping confirmation within 2 hours of departure, on-time arrival confirmation at the first scheduled stop, customer-acknowledgment of the substitution after the fact), name the follow-up event, the time window, and the owner. A Conditional Release without a closed loop is a Release in disguise
9. **Run the trust and evidence check** — Before finalizing, scan the brief for: (a) any divergence claim that lacks a captured artifact (a stated mismatch must be tied to a CDL image, a plate image, a recording, a portal log, or an explicit dispatcher observation), (b) any disposition harder than Release that lacks a stated reason, (c) any escalation language that names or accuses an individual rather than describing a divergence, (d) any closed-loop follow-up that lacks an owner and a time window, (e) any customer-comms draft that speculates about cause. Flag rather than rewrite — a confidently wrong hold damages the carrier relationship as much as a confidently wrong release damages the customer relationship

**Output requirements:**

- A one-line disposition at the very top: **Release** / **Conditional Release** / **Hold** / **Escalate**, with one short reason
- A **Reconciliation Table** — tendered vs. arrived, line by line, with the Match / Minor / Material / Mismatch flag and the source artifact
- A **Substitution and Freshness Notes** block — two short paragraphs noting whether substitution is in play and whether the upstream vetting is fresh
- An **Indicator Pattern Score** — None / Single / Cluster, with one line per matched family from `claims-fraud-indicators.md`
- A **Carrier Dispatcher Script** — three to six short lines the company-side dispatcher can read on the call
- A **Gate Instruction** — a single sentence the gate can act on
- A **Customer Note (if applicable)** — short factual note for any window slip
- An **SIU / Loss-Prevention Note (if Escalate)** — short factual evidence list, no narrative
- An **Evidence Pack Index** — list of captured artifacts with timestamps and source
- A **Closed-Loop Follow-Up Schedule** — only present if disposition is Conditional Release; named follow-up events with owners
- A **Trust and Evidence Check** subsection listing any item that did not pass the check, with the gating action
- Saved to `outputs/` if the user confirms, with the BOL or pickup number in the filename

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- The skill is intentionally narrow to the *physical pickup moment*. The temptation to fold in upstream paper vetting or post-pickup tracking is real but wrong — those workflows have different inputs, different decision authorities, and different evidence preservation rules. Keeping this skill scoped to the pickup window is what makes it usable in the 30-second decision the dispatcher actually has
- The disposition vocabulary (Release / Conditional Release / Hold / Escalate) is non-negotiable. Gate operations and dispatcher operations both depend on a one-word answer; multi-paragraph nuance is read as confusion. The reasons are short for the same reason. If maintainers feel the urge to add a fifth disposition, the better move is a sharper Conditional Release with a clearer follow-up rule
- The indicator-pattern scoring borrows the same four-family lens (`claims-fraud-indicators.md`) used by the claims and carrier-screening skills. That consistency is deliberate — the same operator seeing the same lens across the workflow learns to act on it faster. Maintainers updating one family should update all three skills' references in the same change
- The brief deliberately does not embed any specific carrier-identity vendor name, digital-ID network, or telematics product. The evidence-channel list (CDL image, plate image, walkaround photos, digital-ID token, telematics ping) is vendor-agnostic, and the company's actual feeds belong in `config.yml` rather than in the skill body so the skill survives a vendor change
- Voice on the dispatcher script and the carrier-side communication is calm and procedural for a reason. The 2026 fraud coverage repeatedly notes that pretextual urgency (false time pressure, manufactured "I will lose this load if you do not release in five minutes" framing) is itself a pattern signal. The script is built to slow that pressure down, not to match it
