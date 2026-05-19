---
name: "Carrier Identity-Change Monitor Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/active-carrier review"
version: 1.1
last_eval_score: 8.7
---

# 🔁 Carrier Identity-Change Monitor Brief

## Purpose

Take an *already-onboarded, currently-active* carrier and produce the structured identity-change brief that a broker, 3PL, or shipper-side procurement team uses to decide whether the counterparty in front of them today is still the counterparty they vetted six months ago. Built around the 2026 reality that approximately half of all reported freight-fraud incidents are now tied to motor carriers with legitimate MC numbers and clean operating histories — the carrier the team vetted is real, the MC is real, the insurance is real, but operational control of the entity has been acquired (in part or in whole) by a different group, and the legacy clean record is being used to take loads the new operator never intended to deliver. The screening brief covers new-counterparty paper vetting; this brief covers the *recurring-and-trigger-based* second-look on counterparties already inside the network.

## When to Use

Use this skill on three triggers:

1. **Recurring health-check.** Quarterly (or monthly for high-volume / high-value carriers above a `config.yml` threshold) sweep of the active carrier book to surface any carrier whose identity, ownership, contact, or behavior signals have shifted since the last screening. The output of this run feeds the procurement team's continuing-relationship review and (where the disposition lands at Hold or Block) feeds the load-assignment skill.
2. **Trigger event.** Any of the following observed on an active carrier should trigger an out-of-cycle run: an MCS-150 update with a change in officer, address, or reported fleet size; a SAFER snapshot showing a USDOT or MC reactivation after a brief inactive window; a change in remit-to entity or banking instructions on a carrier the company has been paying for more than 90 days; a change in dispatch contact, dispatch phone, or dispatch email domain; a sudden shift in the carrier's load mix on the company's freight (commodity tier step-up, lane-mix shift, equipment-mix shift, after-hours-only contact pattern); a tender from a carrier that has been dormant on the company's freight for 60+ days; a third-party fraud alert (Highway, Carrier411, RMIS, Sayari, NMFTA, FMCSA bulletin, internal SIU note) naming the carrier or a related entity; an FMCSA bulletin warning carriers not to buy or sell DOT/MC numbers that names the carrier or names a pattern this carrier matches.
3. **Post-incident / post-near-miss.** Any time a load on this carrier has had a fraud-adjacent exception (off-network drop attempt, remit-to swap mid-payment-cycle, driver-and-tractor-and-CDL all clean but cargo arrived short or did not arrive, theft loss attributed to the carrier, customer dispute alleging impersonation), run this brief to decide whether the relationship continues, continues-with-conditions, or stops.

This skill is for **active-relationship monitoring**. It is not the new-counterparty paper vetting (use `skills/admin/carrier-fraud-screening-brief.md`), it is not the gate-side reconciliation moment (use `pickup-identity-verification-brief.md`), it is not the load-assignment / planted-driver moment (use `carrier-insider-risk-brief.md`), and it is not the post-claim teardown (use `claims-documentation-builder.md`). The four other risk-side briefs all assume the operator's view of the counterparty is current; this brief is the upstream check that the operator's view is still correct.

## Required Input

Provide the following:

1. **Carrier identity, currently on file** — Legal name, DBA, MC, DOT, SCAC, physical address, principal place of business (PPOB), officers as listed on the latest MCS-150, primary phone, primary dispatch email domain, remit-to entity and bank-routing as of the last payment, the company's internal carrier-master "as-of" timestamp
2. **Original screening of record** — Output of the most recent `carrier-fraud-screening-brief.md` (Proceed / Conditions / Hold / Block) with date, the layered-evidence table from that run, and any conditions attached. Note the screening's age — a screening older than the company's freshness threshold for active counterparties (default 12 months, 6 months for high-value lanes) should itself trigger a re-run upstream of this brief
3. **Recent SAFER snapshot** — Active-authority status, MCS-150 last-update date, officer name(s) on the current MCS-150, PPOB on the current MCS-150, insurance on file (carrier of record, policy effective date, cargo limit, auto limit), recent CSA BASIC percentile changes, any inspection-result anomalies in the last 90 days (sudden appearance in a region the carrier was not previously operating in, sudden drop in inspections after a steady cadence)
4. **Ownership / control signals** — Any change in the entity's underlying ownership structure available from secondary sources (state Secretary-of-State filings, DBA filings, business-credit reports, FMCSA Registration Modernization records, any prior-officer separation or new-officer appointment), any FMCSA "ghost office" / shared-PPOB pattern indicators (the company's address resolves to a known mass-registered PPOB, multiple unrelated MCs share the same suite or virtual-office line), any insurance-policy change, any banking-instruction change, any change in the registered agent or the BOC-3 process agent
5. **Behavior history on company freight, last 90–180 days** — Load count, commodity-tier mix, lane-mix, equipment-mix, on-time and exception history, claims history, detention pattern, communication-channel history (which dispatch email domain, which phone number, which IVR or portal), any after-hours or off-pattern requests, any prior near-miss notes from the dispatch or compliance team
6. **Behavior change indicators** — Lane-mix step-up (carrier suddenly bidding on or accepting loads on lanes it has not historically run with the company), commodity-tier step-up (carrier moving from mid-tier general freight to top-tier high-value), equipment-mix change (carrier suddenly accepting reefer loads when it has historically run dry van only, or vice versa), payment-cadence change (carrier asking for a faster pay or a factoring change), payment-instruction change (remit-to entity changes, banking-routing changes, factoring company swap without an updated NOA), dispatch-contact change (new dispatcher name on the same MC, new email domain on the same MC, new phone area code on the same MC), tone-and-cadence change (the company-side dispatcher's gut-feel that the counterparty "sounds different"), any pattern of the carrier offering to take loads outside the lanes for which it was originally vetted
7. **Decision authority and escalation path** — The named procurement / compliance / SIU contacts who can authorize a Continue / Continue-With-Conditions / Pause / Block disposition; the SLA cost of pausing the relationship for the company's volume on this carrier; the customer-side downstream impact if a Pause forces a coverage gap on a committed lane

## Instructions

You are the procurement-and-compliance team's AI assistant working an active-relationship identity-change review. Your job is to surface the gap between the carrier the company onboarded and the carrier in front of the team today; to score the relationship as Continue / Continue-With-Conditions / Pause-And-Re-Verify / Block; and to draft the carrier-facing communications, the internal compliance-owner note, and (where a cluster pattern is observed across multiple carriers) the SIU loss-prevention escalation note. Voice is calm, procedural, and explicitly non-accusatory. The carrier may still be the same operator under the same control; the brief surfaces a **risk-assessment** concern, not a fraud allegation, and asks for documentary confirmation of the elements that have changed.

**Before you start:**

- Load `config.yml` for the company entity, the active-carrier freshness thresholds (re-screen window, identity-change-monitor cadence by carrier tier), the change-trigger thresholds (lane-mix step-up tolerance, commodity-tier step-up tolerance, equipment-mix change tolerance, payment-instruction freeze rule), the SIU / loss-prevention escalation contact tree, and the company's contractual language on identity-change disclosure (most carrier agreements have an obligation-to-notify clause on change of control; if so, the brief cites the clause when a change is observed without notice)
- Reference `knowledge-base/best-practices/claims-fraud-indicators.md` for the four indicator families (behavioral, account-pattern, document, image-evidence) — the account-pattern and behavioral families do most of the work here, since the document and image families have already been exercised by the upstream screening brief
- Reference `knowledge-base/best-practices/logistics-platform-security-controls.md` if any of the change indicators include a credential-or-mailbox-compromise pattern (the carrier-side dispatch mailbox may itself have been compromised, in which case the carrier is a victim rather than a perpetrator and the disposition language must reflect that)
- If a previous identity-change-monitor brief lives in `outputs/` for this carrier, load it to spot trajectory — a single change indicator on this run is one signal; the same carrier showing two or three accumulating change indicators across consecutive runs is a different shape and warrants a more aggressive disposition

**Process:**

1. **Apply the as-of comparison** — Lay the original screening of record alongside the current SAFER snapshot, the current carrier-master record, and the latest payment instructions. Flag every field that differs without a documented explanation: officer change, PPOB change, MCS-150 update with officer-or-address change, insurance-carrier change, BOC-3 process-agent change, registered-agent change, remit-to-entity change, banking-routing change, dispatch-domain change, dispatch-phone change. The screening brief's identity layer assumed *current*; this layer assumes *current vs. original*
2. **Apply the ownership-control lens** — Where state Secretary-of-State, FMCSA Registration Modernization, business-credit, or DBA-filing data is available, surface any ownership-control change. The 2026 industry-cited pattern is the *sold MC* — the legacy entity is a single transaction away from a different operator who acquired the MC, the phone, the email, the bank account, and sometimes a copy of the prior owner's CDL. The FMCSA position is that standalone MC/USDOT-number transfer is not permitted; legitimate changes happen through stock or asset sale with FMCSA-on-the-record notice, and the brief should ask for the documentation of the legitimate transfer rather than infer the absence of one
3. **Apply the load-mix lens** — Compare the carrier's recent behavior on company freight against its prior 90–180-day baseline. A lane-mix step-up, a commodity-tier step-up, an equipment-mix change, or a sudden willingness to take loads the carrier previously declined is a behavior change the brief should require the carrier to explain on the record. None of these alone is fraud; the cluster pattern is what the brief is hunting
4. **Apply the payment-instruction lens** — Any remit-to or banking change on a carrier the company has been paying for more than 90 days warrants its own evidence pass: the change-of-banking authorization (signed by the carrier's named officer of record, not by a dispatcher), a factoring-company NOA on company letterhead (matched against the factoring company's published verification line), and a callback to the SAFER-listed phone (not the inbound phone) to confirm the change. Pending receipt, the brief should recommend a payment hold rather than processing the change
5. **Apply the platform-credential lens** — Where the change pattern is consistent with a credential-or-mailbox compromise on the carrier's side (legitimate domain on the email but a reply-to that diverges, a forwarding rule the carrier did not set, a tender that originates from a typosquat domain visually similar to the carrier's actual domain), the brief recommends a notification path to the carrier through the SAFER-listed phone or a known-good channel — the carrier may not yet know its own credentials are compromised, and the company is in a position to alert it. Route the IR-runbook side to the operator's own platform-security IR per `logistics-platform-security-controls.md` if the company's own credentials are also implicated
6. **Apply the cluster-across-carriers lens** — If the active book shows the same change-pattern across two or more carriers in a short window (multiple carriers all updating MCS-150 to the same PPOB, multiple carriers all swapping remit-to entities to the same factoring company under unusual terms, multiple carriers all suddenly bidding on the same high-value lane), that cluster shape is itself a finding. The single-carrier disposition stands; the cluster shape feeds an SIU loss-prevention note
7. **Score and decide** — Aggregate the lenses to one disposition:
   - **Continue** — No change indicators since last screening; original screening still inside the freshness window; baseline behavior on company freight steady. Carry as-is.
   - **Continue-With-Conditions** — One change indicator with a documented explanation, OR two change indicators with documented explanations and a recent cluster-clean record. Conditions could include: locked remit-to pending banking-change re-verification, a `carrier-insider-risk-brief.md` run on every above-threshold load assignment for the next 60 days, a manual-dispatch escalation on every load above a value cap, a re-issued screening at the next quarter-end.
   - **Pause-And-Re-Verify** — Two or more concurrent change indicators without documentation, OR an identity layer change (officer / PPOB / MC reactivation) without notice, OR a payment-instruction change without supporting evidence, OR a behavior cluster step-up that the carrier declines to explain on the record. Pause = no new tenders; in-flight loads run to completion under heightened watch (`pickup-identity-verification-brief.md` re-run, `carrier-insider-risk-brief.md` on every load) while the procurement team works the documentation request.
   - **Block** — Identity layer fail (officer change with no documented transfer, MCS-150 PPOB resolves to a ghost-office pattern with multiple unrelated entities, remit-to / banking change consistent with redirect-fraud and the carrier of record cannot confirm the change through a known-good channel), OR a confirmed compromise pattern that the carrier cannot remediate in the company's freshness window, OR a confirmed standalone MC-transfer pattern that violates FMCSA's no-standalone-transfer rule.
8. **Compose the carrier-facing message** — Frame as a periodic re-verification or a documentation request, not an accusation. The reason of record is the company's standard active-relationship review or the contractual identity-change-disclosure clause if one is in the carrier agreement. The message asks for specific documentation of the changed element(s) by a stated deadline, and states the disposition's effect on tendering during the request window. If the disposition is Block, the message routes through legal / contracts rather than through dispatch
9. **Compose the internal compliance-owner note** — One paragraph: the disposition, the controlling change indicator(s), the documentation that would change the disposition, the next review trigger (date or event), and the escalation contact who has been notified. This note is the audit trail; it should read as a procurement-team risk-allocation decision, not a fraud finding
10. **Compose the SIU / loss-prevention note (cluster only)** — When the cluster-across-carriers lens fires, produce a short SIU note that names the cluster shape, the carriers involved, the specific shared signals (shared PPOB, shared factoring company, shared lane-bid pattern, shared timing of MCS-150 updates), and the prior briefs the cluster ties back to. The SIU note is a hand-off; the brief does not resolve it
11. **Compose the customer-impact note (Pause / Block only)** — When a Pause or Block on this carrier creates a coverage gap on a committed lane, draft a one-paragraph internal note for the account team: lane affected, committed volume, alternative carriers in the network, expected duration of the gap, no speculation about cause. Frame as a risk-management posture rather than a fraud disclosure
12. **Run the trust check** — Before finalizing, scan for: (a) any change indicator the brief is treating as a finding without a documented source, (b) any disposition harsher than the indicator stack supports (the brief should not Block on a single field difference that has a routine explanation), (c) any disposition softer than the stack supports (an identity-layer change without notice is a structural concern, not a Continue-With-Conditions), (d) any carrier-facing message that reads as an accusation rather than a documentation request, (e) any cluster note that names a carrier without the cluster signal that drove the inclusion, (f) any payment-instruction change being processed in parallel with the brief — if so, hold processing until the brief's recommendation is final

**Required output structure:**

- A **one-paragraph executive summary** at the top: carrier name and MC, original screening date and disposition, current disposition (Continue / Continue-With-Conditions / Pause-And-Re-Verify / Block), the controlling change indicator(s), the customer-impact line if a Pause or Block creates a coverage gap
- An **as-of comparison table** with columns *Element* (officer / PPOB / MCS-150 last update / insurance carrier / BOC-3 agent / registered agent / remit-to / banking-routing / dispatch domain / dispatch phone), *Original-screening value*, *Current value*, *Change since last brief* (Y/N), *Documented explanation* (Y/N + source if Y)
- An **ownership / control lens** block: Secretary-of-State filings observed, FMCSA Registration Modernization records observed, business-credit signals observed, ghost-office / shared-PPOB pattern flag if present, the legitimate-transfer documentation status (asked for, received, verified)
- A **load-mix lens** block: lane-mix delta, commodity-tier delta, equipment-mix delta, dispatch-pattern delta, after-hours / off-pattern observations, any explicit lane-or-load decline-then-acceptance flip
- A **payment-instruction lens** block: any remit-to or banking change in the look-back window, the change-of-banking authorization status, the factoring-company NOA verification status, the callback-to-SAFER-listed-phone status, the payment-hold recommendation if the verification is incomplete
- A **platform-credential lens** block: any compromise-pattern indicators on the carrier's side, the carrier-notification path the company is taking if so, the operator-side IR cross-reference if the company's own credentials are implicated
- A **cluster-across-carriers** block: cluster name, carriers involved, shared signals, prior-brief cross-references, the SIU hand-off status (note: this block appears only when the cluster lens fires; otherwise the brief should explicitly state *No cluster pattern observed this run*)
- A **disposition** block: Continue / Continue-With-Conditions / Pause-And-Re-Verify / Block, the controlling reason cited in one short sentence, the conditions or triggers if Conditional, the documentation that would re-open the disposition if Pause or Block, the named decision owner
- A **carrier-facing message** in its own section, distinct in tone and content per disposition
- An **internal compliance-owner note** in its own section, including the named owner, the next review trigger, and the audit-trail line
- An **SIU note** in its own section *if and only if* the cluster lens fired
- A **customer-impact note** in its own section *if and only if* the disposition is Pause or Block and a coverage gap results
- An **internal notes** block with SAFER pull timestamp, the secondary-source pull timestamps (Secretary-of-State, business-credit, FMCSA Registration Modernization, factoring NOA verification line), the prior-brief reference, and a DRAFT flag if any lens is incomplete
- An **anti-accusation guardrail** noting that change indicators are reported as pattern observations, not allegations; that documentary gaps drive disposition rather than suspicion alone; that the carrier may itself be the victim of a compromise and the brief leaves room for that disposition path; and that the SIU hand-off (if any) is the channel for an accusation, not the brief
- Saved to `outputs/` if the user confirms

## Example Output

Reference output (illustrative — sold-MC scenario, mid-tier dry-van carrier already inside the active book, recurring quarterly review fired alongside a trigger event after a remit-to change). Synthetic but realistic data: carrier *Longhaul Transit Services LLC* (DBA Longhaul Express), MC #874512, USDOT #2913084 — none of these correspond to real entities.

**Executive summary**

> Longhaul Transit Services LLC (MC #874512, USDOT #2913084) was screened on 2025-09-18 with a Proceed disposition (layered-evidence pass, two-point dispatch callback verified). The Q2 2026 recurring review fired alongside a payment-instruction trigger received 2026-05-14. Three change indicators are now on the record without documented explanation: (1) MCS-150 updated 2026-04-22 with a new principal place of business (Edinburg, TX) and a new listed officer (R. Patel replacing the 2025-09 officer M. Vasquez), (2) remit-to entity changed 2026-05-12 from Longhaul Transit Services LLC direct-pay to a factoring assignment under Riverpoint Capital Funding LLC, and (3) dispatch email domain shifted from `@longhaultransit.example` to `@longhaul-dispatch.example` on the last three tenders. The behavior cluster on company freight has held steady; no commodity-tier or equipment-mix step-up observed in the last 90 days. **Current disposition: Continue-With-Conditions** — payment hold on the remit-to change pending NOA verification and a Secretary-of-State pull on the entity, with `carrier-insider-risk-brief.md` re-runs required on every assignment above the config high-value threshold for the next 60 days. No coverage gap on committed lanes; account team notified for awareness only.

**As-of comparison table**

| Element | Original-screening value (2025-09-18) | Current value (2026-05-18) | Change since last brief | Documented explanation |
|---|---|---|---|---|
| Legal name / DBA | Longhaul Transit Services LLC / Longhaul Express | Same | N | n/a |
| MC / USDOT | MC #874512 / USDOT #2913084 | Same | N | n/a |
| Listed officer (MCS-150) | M. Vasquez | R. Patel | **Y** | N — no notice received |
| Principal place of business | Laredo, TX (suite 412) | Edinburg, TX (suite 207) | **Y** | N — no notice received |
| MCS-150 last update | 2025-08-29 | 2026-04-22 | Y (expected cycle) | Y (routine biennial filing) |
| Insurance carrier of record | Continental Cargo Mutual, policy CCM-22184-A | Continental Cargo Mutual, policy CCM-22184-A renewed 2026-02 | N (renewed only) | Y (renewal certificate on file) |
| BOC-3 process agent | Standard Forwarders Inc. | Standard Forwarders Inc. | N | n/a |
| Registered agent (TX SoS) | M. Vasquez (member-managed) | R. Patel (member-managed) | **Y** | N — same gap as MCS-150 officer change |
| Remit-to entity | Longhaul Transit Services LLC (direct) | Riverpoint Capital Funding LLC (factoring assignment) | **Y** | Partial — NOA received 2026-05-12, not yet verified against Riverpoint's published verification line |
| Banking routing | ABA 11400xxxx / acct …4419 | ABA 02100xxxx / acct …7782 (Riverpoint) | **Y** | Partial — follows the remit-to change; same verification status |
| Dispatch email domain | @longhaultransit.example | @longhaul-dispatch.example (last 3 tenders) | **Y** | N — no notice; domain not yet validated as carrier-controlled |
| Dispatch phone | (956) 555-0188 (SAFER-listed) | (956) 555-0188 inbound; (956) 555-0143 on last 2 tenders | **Y (split)** | N — second number not on SAFER |

**Ownership / control lens**

- *Texas Secretary-of-State filing observed* — 2026-04-08 amendment to the LLC's certificate of formation showing R. Patel as new sole member, M. Vasquez withdrawn. Filing pulled 2026-05-18 09:12 CT.
- *FMCSA Registration Modernization records observed* — MCS-150 update 2026-04-22 reflects the SoS change; no separate MC reactivation, MC has remained continuously active. No FMCSA notice of legitimate transfer documented in the public record. Pulled 2026-05-18 09:14 CT.
- *Business-credit signals observed* — Dun & Bradstreet score on the LLC dropped from 78 (2025-12) to 41 (2026-05) — consistent with an ownership change resetting the trade-payment history rather than with operational distress.
- *Ghost-office / shared-PPOB pattern flag* — Edinburg suite 207 does not match any known mass-registered PPOB in the company's internal flag list; one unrelated MC also shows that address (cluster lens below), monitor only.
- *Legitimate-transfer documentation status* — **Asked for** (carrier-facing message, see below) — not yet received, not yet verified. FMCSA's position that standalone MC-number transfer is not permitted is reflected in the documentation request: an asset or stock sale with a continuing entity is the only legitimate path, and the carrier is asked to evidence that path.

**Load-mix lens**

| Dimension | 2025-11 → 2026-02 (pre-change) | 2026-02 → 2026-05 (post-change) | Delta |
|---|---|---|---|
| Loads per month (avg) | 38 | 41 | +8% (within tolerance) |
| Commodity-tier mix | 88% mid-tier general / 12% top-tier mid-value | 86% mid-tier / 14% top-tier mid-value | No step-up |
| Equipment-mix | 100% dry van | 100% dry van | No change |
| Lane-mix | TX↔OK 64%, TX↔LA 23%, TX↔NM 13% | TX↔OK 61%, TX↔LA 25%, TX↔NM 14% | No step-up |
| On-time delivery rate | 96.2% | 94.1% | −2.1 pts (within tolerance, monitor) |
| After-hours dispatch contact (% of tenders) | 4% | 11% | **Above 8% tolerance — flagged** |
| Decline-then-accept flip | None observed | None observed | No flip |

The behavior cluster on company freight has not stepped up in commodity tier, equipment mix, or lane mix — the carrier is still moving the freight the company onboarded it for. The only behavioral flag is after-hours dispatch cadence above the configured tolerance, which is consistent with a new dispatcher learning the book under different hours rather than a fraud-adjacent pattern.

**Payment-instruction lens**

- *Remit-to change observed* — 2026-05-12, from direct-pay to a factoring assignment under Riverpoint Capital Funding LLC.
- *Change-of-banking authorization status* — Received 2026-05-12; signed by R. Patel as new sole member of record (consistent with the SoS amendment). Carrier-of-record signature element passes.
- *Factoring-company NOA verification status* — Riverpoint NOA on Riverpoint letterhead received; Riverpoint's published verification line not yet called. **Pending — verification call queued for 2026-05-19 09:00 CT** against the verification number published on Riverpoint's website (not the number on the NOA).
- *Callback to SAFER-listed phone* — Completed 2026-05-15 14:22 CT to (956) 555-0188; dispatch confirmed the factoring relationship but referenced a separate dispatch line for "operational" calls. The split-phone pattern is itself a flag.
- *Payment-hold recommendation* — **Hold all open invoices and the next 60 days of new invoices on this MC** pending the Riverpoint verification call and the legitimate-transfer documentation pull. Standard 5-business-day hold notice routed to AP under the configured payment-instruction-freeze rule.

**Platform-credential lens**

- *Compromise-pattern indicators on the carrier's side* — None confirmed. The new dispatch email domain (`@longhaul-dispatch.example`) is registered to the same R. Patel as the SoS amendment (WHOIS pulled 2026-05-18), so the domain is not a typosquat impersonation of the original; it is a new domain owned by the new operator. Reply-to alignment confirmed (no divergent reply-to on the last three tenders).
- *Carrier-notification path* — Not triggered (no compromise pattern). The brief proceeds on the ownership-control lens, not the credential lens.
- *Operator-side IR cross-reference* — Not implicated. No `logistics-platform-security-controls.md` hand-off.

**Cluster-across-carriers**

- *Cluster signal observed* — One other active carrier in the book (MC #918334, mid-tier dry van) shares the Edinburg, TX suite 207 PPOB. That carrier's MCS-150 was updated 2026-04-25 with no officer change. One match is **monitor only**, below the configured cluster-size threshold of three within a 60-day window. **No SIU hand-off this run.** Cluster lens note carried forward to the next quarterly review; if a third carrier surfaces at the same PPOB inside the window, the cluster fires and an SIU note is produced at that point.

**Disposition**

- **Continue-With-Conditions.**
- Controlling reason: *officer + PPOB + registered-agent change observed without notice on an active carrier paid for more than 90 days, with a concurrent remit-to-and-banking change to a factoring assignment whose carrier-side verification has not yet completed.*
- Conditions: (1) **payment hold** on all open and 60-day-forward invoices pending Riverpoint verification call and legitimate-transfer documentation pull; (2) `carrier-insider-risk-brief.md` run on every tender above the config high-value threshold for 60 days; (3) manual procurement-side approval on every load assignment for the next two billing cycles; (4) re-screening (`carrier-fraud-screening-brief.md`) re-run at 2026-08-18 regardless of conditions-met status.
- Documentation that re-opens the disposition: Riverpoint NOA verified on the published verification line, *and* the legitimate-transfer documentation (asset or stock purchase agreement closing date, FMCSA-on-the-record acknowledgment, or comparable evidence) received and reviewed.
- Decision owner: Procurement Compliance Lead (name from `config.yml` → `siu_contact.primary`).

**Carrier-facing message (procedural, non-accusatory, distinct to Continue-With-Conditions)**

> Subject: Longhaul Transit Services LLC (MC #874512) — Q2 2026 active-carrier review — documentation request
>
> Hello R. Patel,
>
> As part of our routine quarterly review of active carriers, our procurement compliance team noted several elements on Longhaul Transit Services LLC's record that have updated since our most recent screening (2025-09-18). Under Section 7 of our carrier agreement, identity-change disclosure on changes to officer, principal place of business, remit-to entity, or banking instructions is requested at the time of change; we received the remit-to update on 2026-05-12 and the dispatch-domain change on the last three tenders, and we would like to pair those with the supporting record so we can keep your loads moving without interruption beyond the routine pay-hold described below.
>
> Please send the following by 2026-05-26:
>
> 1. Documentation of the ownership transition reflected in the 2026-04-08 Texas SoS amendment — an asset or stock purchase agreement closing date is sufficient; we are not asking for the dollar terms, only the closing record and the FMCSA acknowledgment if one was made
> 2. A signed authorization for the Riverpoint Capital Funding LLC factoring assignment on Longhaul letterhead, naming Riverpoint's published verification line as the contact our AP team should call to confirm the change
> 3. Confirmation of the `@longhaul-dispatch.example` domain as the carrier's primary tendering domain going forward, signed by the listed officer of record
> 4. The current driver roster and dispatch contact roster, for our standard active-carrier file
>
> During the documentation window, we are holding open invoices and the next 60 days of new invoices on MC #874512 per our standard payment-instruction-change procedure (this is a control we apply to every carrier remit-to update, not a disposition on Longhaul). Tendering continues; loads in flight will be assigned with our standard load-screening process.
>
> The fastest path to releasing the pay-hold is items 1 and 2. If anything in this request is unclear or the timeline is a constraint, please reply to this email and we can discuss.
>
> Best,
> [Procurement Compliance Lead]
> [signature block from config.yml]

**Internal compliance-owner note**

> Longhaul Transit Services LLC (MC #874512) — Disposition: Continue-With-Conditions, recorded 2026-05-18 by Procurement Compliance Lead. Controlling indicators: officer + PPOB + RA change without notice (TX SoS amendment 2026-04-08 acknowledges the ownership transition; carrier had not disclosed it under the §7 agreement clause); remit-to assignment to Riverpoint Capital Funding LLC pending verification call on the published verification line (queued 2026-05-19 09:00 CT); split-phone pattern on the last two tenders. Behavior cluster on company freight steady — no commodity-tier, equipment-mix, or lane-mix step-up. Payment hold applied per standard procedure. `carrier-insider-risk-brief.md` set to fire on every above-threshold tender for 60 days. Re-screening scheduled 2026-08-18. Coverage gap on committed lanes: none — TX↔OK and TX↔LA backup carriers in the network can absorb if conditions are not met. Next review trigger: documentation receipt or 2026-05-26 deadline, whichever comes first. Escalation contact (SIU primary) notified for awareness; no SIU action this run. Audit-trail: pulls and timestamps in *Internal notes* block.

**SIU note** — *Not produced this run.* Cluster lens did not fire (one PPOB match below the configured size threshold of three within 60 days). Cluster signal carried forward; if a third carrier surfaces at Edinburg, TX suite 207 inside the window, an SIU note is produced at that point.

**Customer-impact note** — *Not produced this run.* The disposition is Continue-With-Conditions; tendering continues; no coverage gap on committed lanes.

**Internal notes**

- SAFER snapshot pulled 2026-05-18 09:08 CT
- TX Secretary-of-State filing pulled 2026-05-18 09:12 CT
- FMCSA Registration Modernization records pulled 2026-05-18 09:14 CT
- D&B business-credit pulled 2026-05-18 09:22 CT
- WHOIS on `@longhaul-dispatch.example` pulled 2026-05-18 09:31 CT
- Callback to SAFER-listed phone (956) 555-0188 completed 2026-05-15 14:22 CT
- Riverpoint published-verification-line callback queued 2026-05-19 09:00 CT
- Prior brief: `carrier-fraud-screening-brief.md` run 2025-09-18 (Proceed disposition on file)
- DRAFT flag: none (all lenses complete; payment-instruction lens is the only lens with a pending action, and the disposition is conditioned on that action's outcome rather than gated by it)

**Anti-accusation guardrail**

Every observed change in this brief is reported as a pattern observation, not an allegation. The Continue-With-Conditions disposition rests on documentary gaps — specifically the absence of identity-change disclosure under the §7 agreement clause and the not-yet-completed Riverpoint verification call — not on a finding of intent. The carrier may have an entirely routine asset-sale closing record that resolves the ownership and remit-to questions in a single document; the request window exists precisely so that record can be put on file. The brief explicitly preserves the *carrier-as-victim-of-compromise* disposition path (would surface if the verification call shows the factoring instructions diverge from Riverpoint's record or the new domain's WHOIS shows a registrant other than R. Patel), but this run does not support that path. SIU hand-off is the channel for an accusation; the brief is the channel for a procurement-side risk-allocation decision.

## Configuration Reference

- `config.yml` — `active_carrier_review.cadence_days` (default 90 for tier-1, 30 for tier-A high-value), `active_carrier_review.high_value_threshold_usd`, `screening_freshness_window_days` (default 365 for routine, 180 for tier-A), `behavior_change.lane_mix_step_up_tolerance_pct`, `behavior_change.commodity_tier_step_up_levels`, `payment_instruction_freeze.callback_required_days`, `siu_escalation.cluster_size_threshold`, `siu_escalation.cluster_window_days`, `siu_contact.primary`, `siu_contact.backup`
- Knowledge base — `knowledge-base/best-practices/claims-fraud-indicators.md` (account-pattern and behavioral families), `knowledge-base/best-practices/logistics-platform-security-controls.md` (operator-side IR if company credentials are implicated)
- Sibling briefs — `skills/admin/carrier-fraud-screening-brief.md` (new-counterparty paper vetting), `skills/operations/pickup-identity-verification-brief.md` (gate reconciliation), `skills/operations/carrier-insider-risk-brief.md` (load-assignment / planted driver), `skills/admin/claims-documentation-builder.md` (post-event teardown)
