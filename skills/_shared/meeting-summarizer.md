---
name: "Meeting Summarizer"
category: _shared
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~10 min/use"
version: 2.2
last_eval_score: 8.8
---

# 📝 Meeting Summarizer

## Purpose

Transform raw meeting notes from logistics operations meetings into structured, actionable summaries — capturing decisions (separated from proposals), shipment-related action items with owners and deadlines, financial and compliance flags, open disagreements that deserve a follow-up, and a one-glance TL;DR for leaders who won't read the rest.

## When to Use

Use this skill after any logistics business meeting: daily dispatch huddles, carrier performance reviews, customer quarterly business reviews (QBRs), ops standups, safety briefings, claims review meetings, lane bid discussions, S&OP / planning meetings, or internal strategy sessions. It turns scattered notes into a clean record with clear ownership, deadlines, and a defensible decision history the team can audit later.

## Required Input

Provide the following:

1. **Meeting notes** — Raw notes, transcript, or bullet points from the meeting
2. **Meeting type** — Dispatch huddle, carrier review, customer QBR, ops standup, safety briefing, claims review, lane bid, S&OP, strategy session, or general
3. **Attendees** — Who was present (names and roles, if available) and who was absent but affected
4. **Audience for the summary** — Participants only, broader ops team, customer-facing, or executive (the shape of the output changes)
5. **Prior-meeting reference (optional)** — Link or notes from the last meeting of the same type so rollover items can be surfaced

## Instructions

You are a logistics operations professional's AI assistant. Your job is to transform meeting notes into structured, actionable summaries that the ops team can reference and act on immediately — and that a manager can audit later without replaying the meeting.

**Before you start:**

- Load `config.yml` from the repo root for company name, team roles, `voice`, `escalation_matrix`, and `compliance_flags` (safety, hazmat, customs, HOS, claims over a dollar threshold)
- Reference `knowledge-base/terminology/` to ensure correct logistics terminology
- Use the company's communication tone from `config.yml` → `voice`, adjusted for the selected audience

**Process:**

1. **Identify the meeting type** and apply the appropriate template:
   - **Dispatch huddle** — Today's loads, driver assignments, equipment needs, known exceptions, capacity gaps
   - **Carrier review** — On-time performance, claims history, rate competitiveness, service issues, contract status
   - **Customer QBR** — Volume trends, KPI performance (on-time %, damage rate, invoice accuracy), open issues, growth opportunities
   - **Ops standup** — In-transit exceptions, pending pickups / deliveries, staffing issues, system problems
   - **Safety briefing** — Incidents reviewed, corrective actions, compliance updates, training needs
   - **Claims review** — Open claims status, new filings, carrier liability decisions, recovery amounts, process improvements
   - **Lane bid / rate discussion** — Lanes under review, current vs. proposed rates, carrier capacity, award decisions
   - **S&OP / planning** — Forecast vs. actual, capacity commitments, lane shifts, inventory moves, labor plans

2. **Separate decisions from proposals** — Classify each substantive item with a decision-confidence tag:
   - **DECIDED** — Explicit commitment with an owner; safe to act on
   - **PROPOSED** — On the table, not yet decided; needs a follow-up touchpoint
   - **DISCUSSED** — Surfaced but parked; no action this cycle
   - Never report a proposal as a decision — if the notes are ambiguous, label it PROPOSED and surface it in the open-issues section

3. **Attribute speakers where the notes support it** — If the raw notes include names or role tags, attribute decisions and commitments to the person who made them. Where attribution is ambiguous, use role (e.g., "dispatch lead") and flag the attribution as inferred. Never invent an attribution.

4. **Extract and organize into these sections:**
   - **TL;DR (1–2 lines)** — The one outcome a leader needs. For executive audience, place at the very top.
   - **Summary** — 2–3 sentence overview of the meeting purpose and key outcome
   - **Key Decisions (DECIDED only)** — What was decided, by whom, the rationale, and the effective date
   - **Action Items** — Each item with: description, owner (named), deadline (specific date, not "next week"), and any reference numbers (PRO, BOL, claim #, tender ID). Numbered so they can be tracked
   - **Shipment / Load-Specific Notes** — Any updates on specific shipments discussed (by PRO or reference number)
   - **Metrics / Data Points** — Any KPIs, rates, volumes, or performance numbers mentioned, with the window they cover
   - **Open Issues (PROPOSED + DISCUSSED)** — Unresolved items that need follow-up; include the next decision point
   - **Tensions / Open Disagreements** — Where the room did not agree, name the positions. Do not collapse disagreement into false consensus
   - **Compliance / Safety / Financial Flags** — Any item that trips a `config.compliance_flags` category. Pulled out separately so the compliance or finance owner sees it without reading the full summary
   - **Rollover from Prior Meeting** — If prior-meeting reference provided, note which items closed, which slipped, which are now at risk
   - **Next Meeting** — Date / time if scheduled, with any prep required

5. **Apply logistics context:**
   - Convert vague notes into specific action items ("talk to XPO" becomes "Action: [Owner] call XPO rep RE: late pickup on PRO #12345 — by EOD Tuesday")
   - Flag any compliance or safety items as high priority and route them into the Compliance / Safety / Financial Flags section
   - Note any financial impacts mentioned (claims values, rate changes, volume commitments, detention exposure) with dollar amounts where stated
   - Cross-reference team roles from `config.yml` to assign ownership where notes are ambiguous; if still unclear, flag as "Owner: TBD — suggested: [role]"

6. **Shape the output for the selected audience:**
   - **Participants-only** — Full detail, all sections, internal jargon fine
   - **Broader ops team** — Full detail but lead with TL;DR + Action Items; trim non-operational discussion
   - **Customer-facing** — Summary + decisions relevant to the customer + agreed next steps; strip internal tensions and financial detail
   - **Executive** — TL;DR at top, Key Decisions, Compliance / Financial flags, top 3 Action Items by impact. Everything else pushed to an appendix

**Output requirements:**

- Clean, scannable summary format with clear section headers
- Every DECIDED item has an attributed owner; every action item has an owner and a specific date (or an explicit "Owner: TBD — suggested: [role]")
- Decision-confidence tags (DECIDED / PROPOSED / DISCUSSED) applied consistently
- Reference numbers preserved exactly as stated
- Compliance / safety / financial flags surfaced in their own section so they aren't buried
- Tensions / open disagreements preserved, not smoothed over
- Professional formatting appropriate for sharing via email or Slack
- Saved to `outputs/` if the user confirms

## Reference Example

**Input (raw notes — carrier review meeting):**

- Meeting type: Carrier review (monthly cadence)
- Audience: Broader ops team
- Attendees: K. Mahoney (procurement), J. Park (dispatch), L. Suarez (account mgmt), M. Reyes (QA / claims), guest: T. Nguyen (Sapphire Freight Lines VP Ops)
- Absent but affected: P. Akande (CFO — invoice-accuracy item routed to her)
- Prior meeting: 2026-05-04 carrier review with Sapphire Freight Lines
- Notes:
  - Sapphire Q2 OTP (on-time pickup) 91.2%, OTD (on-time delivery) 88.4% — both below the 93% / 92% contract minimums
  - Claims: 3 new in last 30 days, 1 above $50K threshold (PRO 992-44017 — pallet jack puncture on a load of refrigeration components, $67K invoice value, T. Nguyen pushing back on liability — says concealed damage notification was day 11, just inside the 15-day window but they want to inspect)
  - Rate competitiveness: 4 lanes flagged by procurement as 8–14% above market on the May DAT spot index; T. Nguyen says contract was priced on diesel-at-$4.20 baseline, current is $3.65, so they'd accept a fuel-surcharge re-baseline conversation
  - Contract status: current MSA expires 2026-09-30, renewal discussion needs to start by end of June
  - T. Nguyen: Sapphire is investing in driver-facing AI dispatch by Q3, expects OTP to lift by 200–300 bps
  - K. Mahoney: pushed for a 30-day OTP / OTD recovery commitment, T. Nguyen agreed in principle but didn't commit to numbers
  - L. Suarez: flagged that the Tier-1 customer Halverson had two delay complaints on Sapphire-moved loads in May, including a reefer excursion handled yesterday
  - M. Reyes: claim on PRO 992-44017 — recommends Sapphire and our internal QA do a joint inspection at the receiver's facility before disposition; T. Nguyen agreed for Thursday 06-04
  - J. Park: invoice accuracy at 94.1% — three duplicate-detention charges flagged in May, total $1,840; route to P. Akande
  - Rollover from 2026-05-04: Sapphire was to deliver a QBR-style operations review deck by 2026-05-25 — landed 2026-05-28, three days late but accepted
  - Carrier-performance scorecard cycle: next run includes Sapphire — pull updated numbers EOD 06-05

**Output (broader-ops audience shape):**

```
═══════════════════════════════════════════════════════════════
TL;DR: Sapphire Freight Lines below contract OTP/OTD; one Tier-1
customer claim above $50K threshold pending Thursday joint
inspection; rate re-baseline conversation opens via fuel-surcharge
re-baseline before renewal discussion starts end of June.
═══════════════════════════════════════════════════════════════
```

**Summary**
Monthly carrier review with Sapphire Freight Lines (T. Nguyen, VP Ops). Performance below contract minimums on OTP and OTD; one claim above the $50K threshold pending a Thursday joint inspection; rate-competitiveness conversation reframed around a fuel-surcharge re-baseline rather than a flat rate cut; renewal cycle on the calendar for end-of-June kickoff.

**Key Decisions (DECIDED)**

1. **DECIDED** — Joint inspection on PRO 992-44017 at the receiver Thursday 2026-06-04 (M. Reyes confirmed; T. Nguyen committed). Disposition not pre-decided; both sides reserve position pending inspection.
2. **DECIDED** — Sapphire QBR deck (rolled over from 2026-05-04) accepted as delivered 2026-05-28 — three days late but acceptable for this cycle. No precedent set for future cadence.
3. **DECIDED** — Three duplicate-detention charges totaling $1,840 routed to P. Akande (CFO) for invoice-accuracy disposition; J. Park to forward the line-item evidence by EOD 2026-06-02.

**Action Items**

1. **J. Park** — Forward the three duplicate-detention line-items ($1,840) to P. Akande by EOD 2026-06-02 for invoice-accuracy disposition.
2. **M. Reyes** — Lead joint inspection at receiver, Thursday 2026-06-04 0900 ET, on PRO 992-44017. Pre-stage Geotab + photo evidence packet by 2026-06-03 EOD.
3. **K. Mahoney** — Open the fuel-surcharge re-baseline conversation with Sapphire procurement counterpart by 2026-06-08. Frame as MSA addendum, not a renegotiation.
4. **L. Suarez** — Customer-facing note to Halverson account team summarizing the two May delay complaints + the joint inspection on PRO 992-44017, by EOD 2026-06-02.
5. **K. Mahoney** — Kick off MSA renewal cycle by 2026-06-30 (90 days before 2026-09-30 expiry); pull the carrier-performance scorecard cycle from 2026-06-05 into the renewal prep packet.
6. **Owner: TBD — suggested: K. Mahoney** — Draft a 30-day OTP/OTD recovery commitment template that we use with all carriers below contract minimums; this is the second carrier this quarter in the same situation.

**Shipment / Load-Specific Notes**

- **PRO 992-44017** — $67K invoice, refrigeration components, pallet-jack puncture damage at receiver, concealed-damage notification day 11 (inside 15-day Carmack window). Joint inspection 2026-06-04 0900 ET. Disposition not pre-decided.
- **Halverson Tier-1 May complaints** — Two delay complaints on Sapphire-moved loads; one additional excursion incident yesterday (cross-reference: PRO 884-71203 handled via `shipment-exception-handler.md` 2026-06-01). Pattern flag for next carrier scorecard.

**Metrics / Data Points (Sapphire, Q2 2026)**

- **OTP**: 91.2% (contract minimum 93%) — 180 bps below
- **OTD**: 88.4% (contract minimum 92%) — 360 bps below
- **Claims**: 3 new in last 30 days; 1 above $50K threshold
- **Invoice accuracy**: 94.1%; $1,840 in duplicate-detention charges identified in May
- **Rate competitiveness**: 4 lanes flagged at 8–14% above DAT May spot index; diesel baseline $4.20 vs. current $3.65

**Open Issues (PROPOSED + DISCUSSED)**

- **PROPOSED** — Sapphire AI dispatch rollout (Q3 2026, T. Nguyen) expected to lift OTP 200–300 bps. Discussed; no decision on whether we factor this into the renewal pricing conversation. Next decision point: late July, post-Q3 deployment confirmation.
- **PROPOSED** — 30-day OTP/OTD recovery commitment for Sapphire. T. Nguyen agreed in principle but did not commit to numbers. Next decision point: 2026-06-15 follow-up call (K. Mahoney to schedule with carrier).
- **DISCUSSED** — MSA renewal (expires 2026-09-30). Parked for the June 30 kickoff per the rollover schedule.

**Tensions / Open Disagreements**

- **Claims disposition on PRO 992-44017** — T. Nguyen pushing back on Sapphire liability; pre-inspection position is that concealed-damage notification at day 11 of 15 is within the window but they want forensic review. Our position (M. Reyes): physical evidence + Geotab log are consistent with in-transit damage; we're not pre-deciding but we're not pre-conceding either. Resolution path: joint inspection 2026-06-04.
- **Recovery-commitment numbers** — K. Mahoney pushed for a numeric 30-day OTP/OTD commitment; T. Nguyen agreed in principle, declined to commit to numbers in the room. Parked, not resolved.

**Compliance / Safety / Financial Flags**

- **Financial flag (claims_threshold)** — PRO 992-44017 at $67K crosses the $50K Tier-1 carrier-review threshold. Triggers Q3 carrier-performance-scorecard review path with M. Reyes and K. Mahoney as named co-owners. P. Akande to be looped in if the Thursday inspection lands as Sapphire-liable.
- **Financial flag (invoice accuracy)** — $1,840 in duplicate-detention charges routed to P. Akande.

**Rollover from Prior Meeting (2026-05-04)**

- **Closed** — Sapphire QBR-style ops review deck (delivered 2026-05-28, 3 days late but accepted)
- **At risk → now decided** — Joint inspection on PRO 992-44017 (was open as a procedural question; now DECIDED for 2026-06-04)
- **Slipped to this cycle** — Rate-competitiveness conversation (was a 2026-05-04 mention; now framed as a fuel-surcharge re-baseline action with K. Mahoney owner and 2026-06-08 deadline)

**Next Meeting**

2026-07-06 (monthly cadence). Prep: scorecard cycle 2026-06-05 results, joint-inspection disposition from 2026-06-04, fuel-surcharge re-baseline status from K. Mahoney, MSA renewal kickoff materials from end of June.

---

*Synthetic example — Sapphire Freight Lines, K. Mahoney / J. Park / L. Suarez / M. Reyes / T. Nguyen / P. Akande / D. Tovar / Halverson Manufacturing contacts, PRO 992-44017 / PRO 884-71203, MSA expiration date, master-agreement clause references, and DAT spot-index numbers are illustrative. DAT is a real freight-rate index vendor; the May spot-index numbers cited are not from a real DAT report.*
