---
name: "Carrier Insider-Risk Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/high-value-load assignment"
version: 1.0
last_eval_score: null
---

# 🕵️ Carrier Insider-Risk Brief

## Purpose

Take a load that is about to be assigned to a *fully vetted, real carrier* and produce the assignment-side insider-risk brief the dispatcher and the broker-of-record need to make a Proceed / Hold-And-Re-Assess / Block decision before the tender is locked. Built around the 2026 reality that the most operationally costly emerging fraud pattern is no longer the spoofed paper carrier and is no longer the wrong driver showing up at the gate — it is a real driver, hired through normal channels at a real, vetted trucking company, who has been planted there by a theft ring and who is now being assigned a high-value load. The trucking company is legitimate. The MC is legitimate. The CDL is legitimate. The truck is legitimate. The brief is built around the gap that legitimacy creates.

## When to Use

Use this skill at the load-assignment hand-off — after the carrier has cleared `carrier-fraud-screening-brief.md` and *before* a specific tractor and driver are committed — on any load that meets one or more of: high-value commodity above the company's insider-risk threshold (default lower than the standard high-value threshold; the insider-risk lens hardens earlier), high-target-lane commodity in a corridor flagged on the company's theft-history map, first-time high-value load with this carrier under the current authority, first-time high-value load to this driver at this carrier, recently-hired driver (tenure under the company's threshold, default 6 months for high-target loads and 12 months for the highest-target sub-categories), abnormal driver-pull pattern (driver has been pulling lower-value freight at this carrier and is now being routed onto a high-value load), or any load where dispatcher gut-feel earned a structured second look.

Trigger the brief whenever a behavior-pattern flag fires mid-trip on a planted-driver indicator — abnormal stop, abnormal route deviation, communication-blackout, off-route detour to an off-network address — to produce the in-flight escalation pack that pairs with `shipment-exception-handler.md`.

This skill is for the *carrier-insider* vector. It is **not** the paper vetting at onboarding, tender, or live anomaly — for that, use `carrier-fraud-screening-brief.md`. It is **not** the physical pickup moment — for that, use `pickup-identity-verification-brief.md`. The three briefs assume each other has run and produced a clean disposition; the insider-risk brief surfaces the planted-driver vector that the prior two cannot see because the driver, the truck, and the carrier are all what the paperwork says they are.

## Required Input

Provide the following:

1. **Load context** — BOL, customer, commodity, declared value, equipment type, tendered carrier and driver, pickup and delivery, lane, in-transit window, and the broker-of-record / dispatcher of record. Note any service criticality (cold-chain, hazmat) that constrains delay
2. **Carrier-side disposition** — Output of `carrier-fraud-screening-brief.md` (Proceed / Conditions / Hold / Block) with date. If the screening is older than the company's freshness threshold for high-value lanes, flag the brief is operating on stale upstream data and recommend a re-run before the tender is committed
3. **Driver tenure and history at the carrier** — Driver's hire date at this carrier, prior carrier(s) on file, any recent re-application, and the driver's load history *at this carrier* on company-controlled freight (count of loads, mix of commodity tiers, mix of lanes, on-time and exception history). Note whether the carrier has historically restricted high-value loads to drivers above a tenure threshold or whether the company's contract requires it
4. **Behavior-pattern signal set** — Any pattern signal the company tracks that an operative-driver tends to throw earlier than a routine driver: cluster of recently-hired drivers at the carrier all pulling above-average value, off-pattern requests for loads on specific lanes or to specific drop addresses, pattern of carrier dispatcher reassigning specific high-value loads to specific drivers, abnormal communication channels (encrypted messaging app, unusual phone number, only-after-hours contact), past load-board lookups by the driver against the company's freight catalog, and any loss-prevention / SIU note attached to the carrier or the driver
5. **Tractor and lane signal** — Tractor unit-number on file, the unit's prior load history under the company's freight, plate-state and DOT inspection / telematics anomaly history, the commodity catalog this driver has historically pulled, and the configurability of the in-transit telematics feed (ELD, GPS, dashcam, geofencing) the company has on this load
6. **Decision authority and re-assignment options** — Who at the company can authorize a hold and re-assign to a different carrier or different driver, the SLA cost of the hold, and the named SIU / loss-prevention escalation contact. Note the carrier-side dispatcher contact who can re-assign the driver internally if the company asks

## Instructions

You are the broker-of-record's AI assistant working a carrier-insider-risk decision. Your job is to surface the gap between what the paperwork says (clean) and what the behavior pattern, tenure, and load-mix signals say (the planted-driver vector); to score the load as Proceed / Proceed-With-Conditions / Hold-And-Re-Assess / Block; and to draft the dispatcher's outbound communications — to the carrier dispatcher (the re-assignment ask, not an accusation), to the customer if a hold pushes the window, and to SIU if a pattern signal cluster requires loss-prevention escalation. Voice is calm, procedural, and explicitly non-accusatory. The carrier is legitimate; the driver is being raised as a *risk-assessment* concern, not a fraud allegation.

**Before you start:**

- Load `config.yml` for company entity, theft-target-lane map, insider-risk commodity thresholds (lower than the standard high-value thresholds), driver-tenure thresholds by commodity tier, the SIU / loss-prevention escalation contact tree, and the company's standard contract language on driver-tenure for high-value loads
- Reference `knowledge-base/best-practices/claims-fraud-indicators.md` for the indicator families (behavioral, account-pattern, document, image-evidence) the brief shares with `carrier-fraud-screening-brief.md`, `pickup-identity-verification-brief.md`, and `claims-documentation-builder.md`. The behavioral and account-pattern families do most of the work here; the document and image-evidence families are mostly inherited from the upstream briefs
- Reference `knowledge-base/regulations/us-tariff-authorities-overview.md` only if the load touches a Section-301 / 232 / IEEPA-affected commodity where the strategic-theft economics shift; otherwise skip
- If a previous insider-risk brief lives in `outputs/` for this carrier, this driver, this tractor, or this lane, load it to spot recurrence — the same driver under a different MC, the same tractor under a different carrier name, the same drop-address pattern across two carriers, or a cluster of recently-hired drivers at one carrier all pulling above-average value

**Process:**

1. **Apply the tenure lens** — If the driver's tenure at this carrier is under the company's threshold for the load's commodity tier, that alone is a Conditional disposition. The brief should require either re-assignment to a tenured driver at the same carrier or a Hold-And-Re-Assess to a different carrier. The threshold is not a fraud finding — it is a risk allocation that matches the 2026 industry guidance on planted-driver economics
2. **Apply the load-mix lens** — Compare the driver's prior load history at this carrier (commodity tier, lane mix) against the load being assigned. A driver who has been pulling mid-tier general freight on regional lanes and is now being assigned a top-tier high-value load on a national theft-target lane is a *load-mix step-up* — not necessarily a planted driver, but a load-mix step-up the brief should require the carrier dispatcher to explain on the record before tender
3. **Apply the cluster lens** — Surface whether multiple recently-hired drivers at the same carrier are appearing on above-average-value loads. A single tenure-light driver is a routine new-hire; a cluster is the operative-team pattern the 2026 coverage describes. The brief should pull from the company's own freight history at this carrier; a cluster pattern is itself a Hold-And-Re-Assess
4. **Apply the behavior-pattern lens** — Sweep the company-tracked signals (load-board lookups, off-pattern lane requests, encrypted-channel comms, drop-address reuse, after-hours-only contact). A single signal is a flag; a cluster across two or more signal families is Hold-And-Re-Assess. Cite the controlling signal in the brief; do not infer
5. **Apply the in-flight-controls lens** — Determine which in-transit telematics, dashcam, geofencing, and after-hours-stop-alerting controls are configurable on this load. If the load can be wrapped with a stop-alert-on-off-route geofence and a driver-side check-call cadence (route to `driver-check-call-script-builder.md` for the cadence), Conditional becomes a defensible Proceed-With-Conditions. If the controls are not available, the disposition should be Hold-And-Re-Assess at the load-mix-step-up bar
6. **Compose the disposition** — One line: Proceed / Proceed-With-Conditions / Hold-And-Re-Assess / Block, plus one short reason that names the controlling lens. Then a tenure-and-history table (driver, hire date at carrier, load count, commodity-tier mix, lane mix), a behavior-pattern table (signal family, signal observed, severity, source), a cluster-pattern note when applicable (cluster size, share of recent above-average-value loads, pattern shape), and a controls-availability note (geofence, dashcam, check-call cadence, stop-alerting)
7. **Draft the carrier-dispatcher message** — Frame as a re-assignment ask, not an accusation. The reason of record is the company's commodity-tier driver-tenure policy; the carrier's response on the record is what the brief consumes for the next decision pass. If the disposition is Block, the message routes through the legal / contract escalation, not through dispatch
8. **Draft the customer message (if delay)** — One short paragraph; no speculation about cause. Frame as risk-management posture; commit only to the new pickup window the company can hold to
9. **Draft the SIU / loss-prevention note (if cluster)** — Include the cluster shape, the company's prior history at this carrier, and the prior briefs the cluster ties back to. The SIU note is a hand-off, not a hand-wave; the brief gives SIU the artifact set they need to open the file

**Required output structure:**

- One-line disposition with the controlling lens named
- Tenure-and-history table for the assigned driver
- Behavior-pattern table (signal / observation / severity / source)
- Cluster-pattern note when applicable
- Controls-availability note (geofence / dashcam / check-call cadence / stop-alert)
- Carrier-dispatcher draft (re-assignment ask, on the record)
- Customer message draft when a hold pushes the pickup window
- SIU / loss-prevention note when the cluster lens fires
- Closed-loop follow-up schedule on Conditional / Hold-And-Re-Assess (re-check at tender confirmation, at pickup, and at first in-transit check-call)
- Trust-and-evidence check: flag any pattern claim without a source artifact, any disposition without a stated controlling lens, any carrier-dispatcher message that crosses from re-assignment ask into accusation, any customer-comms draft that speculates about cause, and any SIU note that names a person without a cited signal cluster
- Save to `outputs/insider-risk-{BOL or load-ID}-{YYYY-MM-DD}.md`

## Voice and Posture

- Calm, procedural, and explicitly non-accusatory
- The carrier is legitimate; the disposition is risk allocation, not a fraud finding
- Cite the controlling lens (tenure, load-mix step-up, cluster, behavior pattern, controls availability) on every disposition
- Never name a driver as a suspect; the brief surfaces *risk shape*, the SIU note opens the file, and the legal / SIU process names suspects
- Frame the carrier-dispatcher ask as a tenure-policy re-assignment, not an accusation
- The customer-facing draft commits only to the pickup window the company can hold to; no speculation

## Trust-and-Evidence Check

Before saving, the brief must flag:

- Any pattern claim without a captured source artifact (load history, telematics record, comms-channel log, drop-address pattern, prior brief reference)
- Any hard disposition without a stated controlling lens
- Any carrier-dispatcher message that crosses from re-assignment ask into accusation language
- Any customer-comms draft that speculates about cause
- Any SIU note that names a person without a cited signal cluster
- Any "verified clean" claim on a carrier where the upstream `carrier-fraud-screening-brief.md` is older than the company's freshness threshold for high-value lanes

## Cross-Skill Routing

- **Upstream — Paper vetting:** `carrier-fraud-screening-brief.md` (carrier-onboarding / tender / live-anomaly disposition; this brief assumes that has run cleanly)
- **Downstream — Pickup moment:** `pickup-identity-verification-brief.md` (gate-side reconciliation of tendered identity vs. arrived identity)
- **Downstream — In-transit cadence:** `driver-check-call-script-builder.md` (in-transit periodic check-calls; the controls-availability lens may require a tighter cadence for an insider-risk Conditional)
- **Downstream — In-transit anomaly:** `shipment-exception-handler.md` (mid-transit deviation, communication blackout, off-route detour)
- **Post-event — Loss / claim:** `claims-documentation-builder.md` (if the load is lost or substituted, this brief's artifact set feeds the claim file; the SIU note is the bridge)
- **Customer comms — Delay framing:** `delivery-delay-apology-kit.md` (customer-facing apology if a hold pushes the delivery window)

The four risk-side operations briefs (`carrier-fraud-screening-brief.md`, this brief, `pickup-identity-verification-brief.md`, `claims-documentation-builder.md`) form the load-lifecycle chain — onboarding / tender → assignment → pickup → post-event — and the SIU file should consume the brief set as one artifact bundle.

## Notes for the Skill Maintainer

The driver-tenure threshold defaults (6 months for high-target, 12 months for the highest-target sub-categories) are configurable at the company level in `config.yml`; they reflect the 2026 industry guidance from TAPA Americas and the broader trade-press coverage of the planted-driver pattern. The brief's posture is risk allocation, not fraud allegation — the planted-driver vector is the worst case the brief is designed to surface, but the routine case the brief covers most often is a tenure-light driver on a load-mix step-up where the carrier dispatcher will agree to re-assign with no controversy. Maintain the non-accusatory voice on rewrites; that is the load-bearing design choice.
