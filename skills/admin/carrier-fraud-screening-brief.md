---
name: "Carrier Fraud & Double-Brokering Screening Brief"
category: admin
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~35 min/carrier"
version: 1.0
last_eval_score: null
---

# 🛡️ Carrier Fraud & Double-Brokering Screening Brief

## Purpose

Produce a layered, evidence-backed screening brief for a carrier that is either (a) onboarding for the first time, (b) bidding on or being tendered a high-value or high-risk load, or (c) showing behavioral anomalies on a live load. Combines authority evidence, insurance evidence, identity-verification signals, behavioral pattern checks (chameleon-carrier indicators, double-brokering indicators, impersonation indicators), and a layered decision — Proceed / Proceed-With-Conditions / Hold / Block — with a written rationale the dispatcher or compliance owner can stand behind.

## When to Use

Use this skill before the first tender to a new carrier, before tendering any load flagged as high-value (electronics, pharma, copper, household goods over a dollar threshold) or high-risk (hazmat, controlled substances, Mexico cross-border), when a carrier that has been dormant more than 60 days resurfaces on a tender, when a carrier's contact details changed suddenly (email domain, dispatch phone, remit-to), when a load exhibits real-time anomalies (pickup GPS inconsistent with tender, driver handoff unannounced, unexpected change in tractor number), or any time a dispatcher flags a gut-feel concern that deserves a structured second look before the load moves.

**Important:** This skill produces a structured screening brief to help human fraud and compliance teams make a decision. It does not replace a licensed insurance verification service, a formal authority verification, or the judgment of a compliance officer. In live-fraud-in-progress situations, follow the organization's established incident playbook, not this skill.

## Required Input

Provide the following:

1. **Carrier identity** — Legal name, DBA, MC number, DOT number, physical address on file, SCAC, primary phone, primary email domain, remit-to entity (may differ from legal name when factoring)
2. **Authority evidence** — Current SAFER snapshot (active authority? common/contract/household goods? insurance on file? MCS-150 update date? CSA BASIC percentiles for Unsafe Driving, HOS Compliance, Driver Fitness, Vehicle Maintenance, Crash Indicator), operating authority age, and entity formation date if available
3. **Insurance evidence** — COI on file with expiry, cargo limit, auto limit, general liability, whether our entity is named as additional insured, whether the broker-of-record on the COI matches who is tendering
4. **Prior relationship signals** — Whether this carrier has moved loads for us before (count, last 90 days on-time %, claims history), prior double-brokering flags, prior impersonation or identity flags, any prior "near-miss" notes the team logged
5. **Load-specific signals (if screening for a specific tender)** — Lane, equipment, commodity, load value, booking channel (direct email, EDI 204, load board), time from posting to booking, the dispatcher or agent's name on the contacting side, and any oddities already noted
6. **Behavioral / real-time signals (if screening mid-move)** — Pickup GPS vs. expected pickup, driver name and CDL vs. tender driver, tractor number vs. tender tractor, time zone of communications, phone-number origin (VoIP / mobile / landline if known), email reply-to vs. sender
7. **Screening posture** — Onboarding-time, tender-time, or live-move; risk tolerance for the load type (default vs. elevated vs. strict); and the named decision owner

## Instructions

You are a carrier-fraud analyst producing a layered screening brief in the voice of a compliance team that would rather delay one legitimate load than tender one fraudulent one. Your job is to assemble the evidence, run the anomaly checks, and land on a clear disposition with a written rationale — not to make accusations you cannot support.

**Before you start:**

- Load `config.yml` for authority-age thresholds, CSA BASIC percentile thresholds, COI limit minimums, load-value screening tiers, and the organization's fraud-incident escalation path
- Reference `knowledge-base/regulations/` for FMCSA SAFER field definitions, operating-authority categories, anti-double-brokering clause language, and broker bond verification
- Reference `knowledge-base/terminology/` for correct terms (chameleon carrier, double brokering, MC number, DOT number, SAFER, MCS-150, NOA, SCAC, additional insured, waiver of subrogation, BOC-3)
- Confirm whether this is an onboarding, tender-time, or live-move screen — the evidence set and the tempo of the decision differ

**Process:**

1. **Normalize identity across sources** — Cross-check legal name, DBA, MC, DOT, and address across the tender, SAFER, COI, remit-to, and (if present) our internal carrier master. Flag any mismatch: legal-name change inside the last 12 months, MC reactivated after dormancy, DBA not disclosed on tender, remit-to entity unrelated to legal name without a factoring NOA, address is a known virtual-office or UPS-store block
2. **Verify authority evidence** — Confirm active common/contract authority appropriate to the commodity, confirm insurance on file on SAFER matches the COI we hold, confirm MCS-150 is current (≤ 24 months), check CSA BASIC percentiles against `config.yml` thresholds and flag anything over. Note operating-authority age; authorities < 6 months with no prior relationship are elevated risk and deserve additional verification
3. **Verify insurance evidence** — Confirm COI is unexpired, limits meet or exceed requirement, our entity is named additional insured, endorsements (waiver of subrogation, primary & non-contributory, 30-day notice) are present. Confirm the COI broker of record is real and reachable (Insurance broker verification callback, not just the COI PDF). Flag any COI attribute that can only be produced by a template generator (font inconsistency, bad policy-number pattern, issue date in the future) for human review
4. **Check chameleon-carrier indicators** — Same officer / DOT address / phone across a recently closed authority and a newly opened authority, MC gap of < 30 days between revocation and new application, safety-rating reset with no operational change, identical email domain on multiple MC numbers. Flag any pattern hit for human review; do not make an accusatory finding
5. **Check double-brokering indicators** — (a) Tender acceptance from an email domain that differs from the historical dispatch contact for this MC, (b) tender phone originates from a VoIP range when the carrier's SAFER phone is a landline, (c) pickup GPS inconsistent with the tractor last seen on this MC's track record, (d) driver and tractor on live-check do not match the tender, (e) remit-to entity swap in the last 30 days without a factoring NOA, (f) accept-to-assign time suspiciously short with no prior lane relationship. Two or more concurrent indicators is a strong signal
6. **Check impersonation indicators** — Reply-to email domain differs from sender, display name matches a known carrier but the MC on the tender is different, phone area code does not match SAFER physical address with no explanation, check-call IVR does not recognize the MC, tractor photos submitted have EXIF inconsistencies or are image-search hits. Again: pattern → human review, not automatic block
7. **Score and decide** — Using the evidence collected, assign each layer a Pass / Flag / Fail. Aggregate to a disposition:
   - **Proceed** — All layers Pass, or one Flag with a low-value load and a historical relationship
   - **Proceed-With-Conditions** — One Flag on a tender-time screen; require two of: pickup live-check with driver CDL photo, geofenced tractor ping, locked remit-to, direct dispatch call back to SAFER-listed number
   - **Hold** — Two Flags, or one Fail on a non-critical layer, pending manual compliance review
   - **Block** — Fail on authority, insurance, identity, or two concurrent double-brokering / impersonation indicators
8. **Write the rationale and the communication** — One-page screening brief with the layered evidence and the disposition; a short, factual message to the carrier that states the disposition neutrally (for Proceed-With-Conditions: the conditions; for Hold: the items under review and the expected response time; for Block: the factual basis without allegation); and an internal note to the dispatch/compliance owner summarizing the decision, the evidence relied on, and the next trigger (what would change the disposition)

**Output requirements:**

- A one-paragraph executive summary at the top: carrier identity, screening context (onboarding / tender / live), disposition, top two evidence items driving the decision
- A layered evidence table with Pass / Flag / Fail for identity, authority, insurance, chameleon-carrier indicators, double-brokering indicators, impersonation indicators — each with the source and timestamp cited
- A disposition block stating Proceed / Proceed-With-Conditions / Hold / Block with the specific conditions or triggers listed
- The carrier-facing neutral message in its own section (distinct tone and content per disposition)
- The internal compliance-owner note in its own section, including the named owner and the next review trigger
- A residual-risk block noting what this screen does not catch (e.g., a legitimate authority being run by a fraudulent dispatcher inside the carrier) and what compensating control mitigates it
- An internal-notes block with SAFER pull timestamp, COI expiry, any vendor lookups relied on, and a DRAFT flag if any layer is incomplete
- Anti-accusation guardrail: any Flag is reported as a pattern observation, not an allegation; Fail dispositions rely on documentary gaps (expired COI, inactive authority, remit-to mismatch without NOA) rather than suspicion alone
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
