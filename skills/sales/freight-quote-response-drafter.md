---
name: "Freight Quote Response Drafter"
category: sales
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~20 min/quote"
version: 2.0
last_eval_score: null
---

# 💰 Freight Quote Response Drafter

## Purpose

Take an inbound RFQ and the company's internal cost basis and produce a customer-ready quote response — quote letter or email, line-itemized pricing with the accessorial schedule that prevents downstream invoice disputes, transit and equipment commitments, validity window, value-add specific to this lane and customer, plus the internal margin note and a defensible "why this number" file the account manager can hand off without a verbal briefing. Built around the 2026 reality that spot-rate volatility, fuel-surcharge mechanics, and accessorial creep are the three places a quote either wins or quietly loses margin after the fact.

## When to Use

Use this skill when a customer or prospect requests a quote (single-shipment spot, multi-shipment lane, project move, RFQ row response, RFP single-section response) and the team needs a polished, internally defensible response. Trigger it again on a re-quote when the load profile changes (commodity, weight, accessorial, lane, equipment), when a fuel-surcharge basis update has reset the all-in landed cost, or when a counter-offer needs a structured response that does not give margin away one accessorial at a time.

This skill is for *per-quote* responses. For multi-section RFP responses or 3PL bid packages, the brief should be drafted here for each pricing row but the overall response framing belongs in a separate skill (planned). For active-shipment rate inquiries (customer asking about a rate already in motion), use `shipment-inquiry-responder.md`. For spot-versus-contract negotiation strategy that runs across multiple quotes on a lane, use `spot-vs-contract-rate-negotiation-brief.md`. For load-tender responses on tendered freight (EDI 204 / direct-tender), use `load-tender-response-drafter.md`.

## Required Input

Provide the following:

1. **Cost basis** — Carrier line-haul (or internal asset cost), fuel-surcharge basis (percent-of-line-haul, cents-per-mile DOE-indexed, or fixed percentage with the index source named), accessorials with the per-event cost (detention, layover, lumper, sort and segregate, liftgate, residential, inside delivery, appointment, redelivery, reweigh, reclass, hazmat, oversize, expedited, team driver, tarp, blocking and bracing), insurance / cargo coverage cost above carrier liability if applicable, and the margin-target band (floor / target / stretch) for this customer or lane
2. **Shipment / lane details** — Origin and destination (city / state / ZIP, named shipper and consignee if known), commodity description and NMFC / freight class for LTL, weight and dimensions, equipment type (dry van, reefer with temp band, flatbed, step-deck, RGN, intermodal container, air ULD, ocean container size and SCAC if drayage in scope), pickup window with appointment requirement, delivery window with MABD / hard-appointment flag, hazmat UN# and packing group if applicable, special handling (white-glove, two-person, security escort, food-grade wash-out)
3. **Customer context** — New prospect / existing-active / existing-dormant, account tier, volume expectation (single load, lane commitment, season program), competitive situation if known (specific competitors named, prior loss reason, target price the customer has signaled), prior-relationship signals (on-time history with the customer's other carriers, payment-history, credit terms, dispute pattern), incumbent rate of record if available, the named decision-maker and the budget cycle
4. **Service tier alternatives** — What the company can offer beyond the baseline ask: faster transit (expedited TL, team driver, air for a TL move that should not be on the road), slower transit at a discount (intermodal swap, consolidated LTL on a multi-stop pool), guaranteed window (LTL guaranteed, TL appointment-firm, parcel guaranteed), risk-shifted (declared-value uplift, broker-of-record vs. asset-based)
5. **Authority and validity** — Quote validity window (default 7 days for spot, 14 for project, 30 for contract row), the named approver if the rate is below the floor or above a customer-specific cap, the price-protection clause if any (fuel mechanism, GRI pass-through, accessorial change), and the channel the customer expects the quote on (email, customer portal RFQ row, freight-procurement platform, broker-portal)

## Instructions

You are a logistics sales specialist's AI assistant drafting a single-quote response. Your job is to present a competitive number that defends margin, line-item every accessorial that will show up later in the invoice, set a credible transit and equipment commitment, and never quote a rate the company cannot run.

**Before you start:**

- Load `config.yml` for company entity, signature block, brand voice, customer-tier tone specs, accessorial schedule by mode, fuel-surcharge mechanism of record, margin-floor rules, validity-window defaults, and the approval matrix (who signs off on rates below floor, above contract cap, or on new-customer first quotes)
- Reference `knowledge-base/terminology/` for correct terms (line-haul, FSC / FAC, accessorial, MAWB / HAWB / BOL / PRO, MABD, OTIF, dim weight, NMFC, freight class, declared value, released-value limitation, demurrage / detention, drayage, accessorial schedule, GRI, peak-season surcharge)
- Reference `knowledge-base/best-practices/` for any company-specific quoting rules (e.g., "always quote dimensional and actual on parcel and reference the higher", "always reference the fuel index date on long-validity quotes", "never quote a hazmat run without the carrier hazmat-endorsement confirmation")
- Reference `knowledge-base/regulations/us-tariff-authorities-overview.md` if the lane is cross-border and the customer's landed cost depends on a duty stack
- If a previous quote brief lives in `outputs/` for the same customer or lane, load it to spot drift in the customer's ask and to keep the language consistent across rounds

**Process:**

1. **Validate the RFQ inputs** — Flag anything that blocks an accurate quote: missing weight or class on LTL, missing temp band on reefer, accessorial that is conditionally triggered without the trigger condition stated (residential / liftgate / appointment / inside / detention threshold), declared value above the carrier's released-value limitation without an uplift named, hazmat without UN# / packing group / placard rule. List the gaps before pricing — one of the most common quote-failure modes is pricing inside an assumption the customer never agreed to
2. **Build the cost stack** — Line-haul + fuel + accessorials (priced or asterisked-as-event-driven) + insurance uplift if applicable + margin = quote-of-record. Keep accessorials separate from line-haul rather than bundling — a bundled rate invites accessorial disputes at the invoice
3. **Set the validity window and the price-protection clause** — Validity by quote type (default 7 days spot, 14 days project, 30 days contract row). Name the fuel-surcharge mechanism so a movement in the index does not require a re-quote conversation. State whether GRIs and accessorial-schedule changes flow through within the validity window or require a re-issue
4. **Select the service-tier presentation** — Lead with the requested service. If a faster or slower or risk-shifted alternative materially changes the customer's decision (cost gap > X% or transit gap > Y hours), include it as a clearly labeled alternative ("Same lane, intermodal swap: −$420, +28 hours transit") rather than a buried bullet. Do not present more than three options — three is the ceiling that reads as helpful; four reads as a sales tactic
5. **Position the value-add specifically** — Two or three points tied to the customer's actual exposure on this lane: documented OTIF on this lane, claims-handling response time, dedicated-asset availability if the customer has had a churn problem, EDI / API integration if the customer's TMS pain is integration cost, hours of operation if the customer has after-hours pickups, language coverage if it is a cross-border move. Do not present generic value-add ("dedicated account management", "real-time tracking") to a sophisticated buyer — the buyer reads it as filler
6. **Draft the customer-facing quote response** — Subject line that leads with the lane and the rate validity ("Quote — LAX → DFW dry van — valid 7 days"), one-paragraph context sentence, the rate of record with the cost components shown for transparency, the accessorial schedule as a labeled list with per-event prices and trigger conditions, transit time and equipment commitment, the named alternatives if any, the value-add tied to this lane, validity-and-fuel mechanism, and a clear single next step ("Reply to confirm and we will tender by 2 PM PT" / "Happy to walk through this by phone today")
7. **Compose the internal margin note** — Below the customer-facing quote, separated visibly: cost basis line by line, margin in dollars and percent at the quoted price, the floor / target / stretch positions, the competitive read (named competitor and signal if known), the approval status (auto-approved / pending named approver / above ceiling — held), and the risk callout (any assumption that, if wrong, breaks the margin: detention drift, accessorial surprise, fuel-index movement, transit miss with SLA exposure)
8. **Run the trust and margin check** — Before sending, scan for: (a) any rate component without a stated source (line-haul without lane reference, fuel without index, accessorial without per-event price), (b) any "all-in" rate that hides accessorial exposure the invoice will surface later, (c) any commitment (transit, equipment, guaranteed window) the company cannot reliably deliver on, (d) any value-add that would not be true if audited, (e) any rate below the configured floor without the approver named, (f) any validity window that exceeds the company's price-protection mechanism, (g) any cross-border quote whose duty-stack assumption is the customer's responsibility being framed as the company's. Flag rather than silently rewrite — a quote that wins on a wrong commitment is a quote that loses the account on the second load

**Output requirements:**

- A one-line classification header for the reviewer (Customer / Lane / Mode / Service tier / Validity / Margin band)
- A **Customer-Facing Quote Response** in the channel format requested — subject line + body for email, structured row for portal / RFQ tooling, or a 6–10 line callback script for phone follow-up
- A **Pricing Breakdown** — line-haul, fuel surcharge with index named, accessorial schedule with per-event prices and trigger conditions, insurance uplift if applicable, total
- A **Service-Tier Alternatives** block — at most three labeled alternatives with cost / transit / risk gap stated
- A **Value-Add Block** — two or three lane- or customer-specific points
- A **Validity and Price Protection** statement — validity window, fuel mechanism, GRI / accessorial-schedule pass-through rule
- An **Internal Margin Note** — cost basis, margin $ and %, floor / target / stretch, competitive read, approval status, risk callout
- A **Trust and Margin Check** subsection listing any item that did not pass the check
- Saved to `outputs/` if the user confirms, with the customer name and lane in the filename

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Notes for Maintainers

- The line-itemized accessorial schedule is the single highest-leverage discipline in this skill. The 2026 freight-procurement coverage repeatedly identifies accessorial creep and "all-in" ambiguity as the dominant invoice-dispute pattern; pricing every accessorial with its trigger condition up front eliminates the dispute before it starts. Maintainers should not soften the per-event-with-trigger requirement to make the quote feel cleaner
- The three-option ceiling on service-tier alternatives is intentional. Four or more options reads as a sales tactic to a sophisticated buyer; one option reads as a take-it-or-leave-it; two or three reads as helpful. The pattern is the work
- The "named competitor and signal" line in the margin note is for the account manager, not the customer. It belongs only in the internal margin note and never in the customer-facing reply, even on a tier-one account
- The fuel-surcharge mechanism citation is non-decorative. A quote validity window longer than 7 days without a fuel mechanism cited will lose margin to an index movement the company should have priced through; the brief enforces the citation so the price-protection clause does the work the validity window claims
- The interaction with `spot-vs-contract-rate-negotiation-brief.md` is one-directional in the same way the customs-tariff pair works: the negotiation brief consumes per-quote outputs from this skill and gives back the lane / customer-segment positioning that this skill weaves into the value-add block. Maintainers updating one should review the input / output contract on the other only — the analytical bodies are intentionally separate
