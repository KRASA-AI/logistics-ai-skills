# Logistics AI Skills

**Free, open-source AI prompts and workflows built for logistics teams.** Clone this repo, point your AI assistant at it, and start saving hours every week.

> Built and maintained by [KRASA AI](https://krasa.ai) — free AI tutorials and skills for every industry.
> See all industries at [krasa.ai/industries](https://krasa.ai/industries).

---

## What's Inside

This repo is a complete AI toolkit for logistics. Every skill is a standalone prompt file that works with **Claude, ChatGPT, or any major AI assistant** — no coding required.

| Skill | What it does | Time saved |
|-------|-------------|------------|
| Backhaul / Deadhead Reducer | Turn a week's load list, committed outbound lanes, and available equipment into a ranked set of backhaul-pairing and triangulation opportunities — each with estimated empty miles saved, revenue added, driver-hour feasibility, and ready-to-use sourcing actions (post to load board, call target broker, pull from committed shipper pool). | ~45 min/week |
| Carrier Identity-Change Monitor Brief | Take an *already-onboarded, currently-active* carrier and produce the structured identity-change brief that a broker, 3PL, or shipper-side procurement team uses to decide whether the counterparty in front of them today is still the counterparty they vetted six months ago. | ~25 min/active-carrier review |
| Carrier Insider-Risk Brief | Take a load that is about to be assigned to a *fully vetted, real carrier* and produce the assignment-side insider-risk brief the dispatcher and the broker-of-record need to make a Proceed / Hold-And-Re-Assess / Block decision before the tender is locked. | ~30 min/high-value-load assignment |
| Carrier Performance Scorecard | Turn a pile of historical shipment data into a clean, decision-ready carrier scorecard that grades each carrier on on-time performance, damage/claims rate, invoice accuracy, tender acceptance, responsiveness, and cost competitiveness — then recommends lane-level "preferred / backup / probation" tiers so procurement can reshape the routing guide with confidence. | ~45 min/scorecard |
| Carrier Rate Comparison | Normalize quotes from 2–6 carriers onto an apples-to-apples all-in landed-cost basis, show transit-time and service-level deltas alongside the rate math, and produce a ranked recommendation with a one-paragraph rationale — so the shipping manager or CSR can award the load in minutes instead of rebuilding the math in a spreadsheet. | ~20 min/comparison |
| Demand Forecasting Summary Brief | Turn a raw demand forecast (volume by lane, SKU, or region) and recent actuals into a short operations brief that planners, carrier-sourcing leads, and warehouse managers can act on: where volume is trending up or down, where the forecast is diverging from reality, where capacity or inventory needs to move, and what the forecast implies for carrier commitments and labor plans over the next 2–8 weeks. | ~40 min/cycle |
| Dock Scheduling & Detention Prevention Brief | Analyze dock appointment data, carrier arrival patterns, and detention/demurrage invoices to produce an action-ready brief that reshapes the daily dock schedule, cuts avoidable detention charges, and protects customer-critical inbound/outbound flows. | ~30 min/brief |
| Driver Check-Call Script Builder | Take a load that is in transit and produce the driver-side check-call script the dispatcher (or the dispatcher's voice agent) needs to run a periodic, on-cadence verification of position, ETA, exception state, and driver wellness — and to log the response cleanly into the load record. | ~10 min/check-call cycle, ~3-5 hr/dispatcher/day at scale |
| Geopolitical Disruption Shipper Brief | Turn a live geopolitical or maritime corridor disruption — Strait of Hormuz closure, Red Sea / Bab-el-Mandeb attacks, Suez or Panama Canal restriction, Black Sea grain corridor change, port strike, sanctions snap-on, airspace closure — into a shipper-ready brief that names the exposed shipments, ranks the routing alternatives with cost and time deltas, decodes the surcharge stack the carriers are pushing, and produces the customer-facing notice the commercial team can send today. | ~60 min/event/week |
| Last-Mile Pre-Positioning Advisor | Turn a short-horizon demand forecast, weather outlook, and local-event signal set into a concrete last-mile pre-positioning plan: which depots or micro-fulfillment nodes need more inventory, more drivers, or more vehicles by which day, how many of each, and what the dollar downside is if the move is not made. | ~45 min/cycle |
| Pickup Identity Verification Brief | Take a load that is about to be picked up — by a carrier the company has already vetted on paper, but where a real driver in a real tractor is now arriving at a real shipper or yard — and produce the pickup-side verification brief the gate, the dispatcher, and the shipper-of-record need to clear the load with confidence or to hold it. | ~25 min/high-risk pickup |
| Route Optimization Brief | Take a raw stop list for a multi-stop P&D or delivery route and produce a resequenced, driver-ready plan with defensible mileage and time savings, HOS-feasibility checks, and a ready-to-send dispatch communication — so the planner can commit the new sequence in minutes instead of eyeballing a map. | ~30 min/route |
| Shipment Exception Handler | Triage shipment exceptions (delays, damages, refused deliveries, weather holds, customs holds) and produce a structured response plan with root-cause analysis, corrective actions, and ready-to-send stakeholder communications. | ~20 min/exception |
| Shipment Status Summarizer | Take raw tracking events from one or more modes (parcel, LTL, TL, ocean, air, drayage, last-mile) and produce a concise, audience-appropriate status update that normalizes the milestone language, highlights exceptions with their business impact, states a confident or caveated ETA, and surfaces the one or two things that actually need a human decision — so the CSR, account manager, or executive reader gets signal, not a log dump. | ~10 min/update |
| 3PL Multi-Section RFP Response Drafter | Take a multi-section shipper or beneficial-cargo-owner RFP — the kind that runs to dozens of pages across warehouse-management, fulfillment, transportation, KPI reporting, customer service, security, sustainability, references, and pricing rows — and produce a customer-ready response that pulls the right reusable content blocks from the company's RFP-content library, applies the per-RFP customizations the buyer asked for, flags every section that requires subject-matter-expert (SME) input the library cannot satisfy alone, and lands as a polished draft the deal-desk owner can hand to the SME pass without rework. | ~6–10 hr/RFP |
| Freight Quote Response Drafter | Take an inbound RFQ and the company's internal cost basis and produce a customer-ready quote response — quote letter or email, line-itemized pricing with the accessorial schedule that prevents downstream invoice disputes, transit and equipment commitments, validity window, value-add specific to this lane and customer, plus the internal margin note and a defensible "why this number" file the account manager can hand off without a verbal briefing. | ~20 min/quote |
| Load Tender Response Drafter | Evaluate an inbound load tender against carrier or broker acceptance criteria and produce a structured accept / counter / reject recommendation — along with the customer-facing response (EDI 990 equivalent language or email reply) and an internal decision rationale that a dispatcher or sales rep can approve in under a minute. | ~10 min/tender |
| Spot vs. Contract Rate Negotiation Brief | Produce a negotiation-ready brief for a single lane (or a short list of lanes) that compares the current contract rate against the live spot market, quantifies the gap in dollars per load and per quarter, and recommends a renegotiation posture — hold, open for rebid, file a rate-review letter, or move volume — with ready-to-send talking points for both the shipper-side and carrier-side conversation. | ~30 min/lane |
| Delivery Delay Apology Kit | Turn a specific late-shipment situation into a mode-aware customer communication set — the proactive notification that goes out *before* the customer asks, the reply to the inbound "where is my order" inquiry, the carrier-facing callback script that confirms the new ETA, and the internal note that tells ops what was promised. | ~15 min/delay event |
| Shipment Inquiry Responder | Take a customer's inbound shipping question and the shipment data the team already has, and produce a customer-ready reply on the channel the customer used — plus the internal note that tells the next shift what was promised. | ~10 min/inquiry |
| BOL Generator | Turn raw shipment details into a VICS-aligned Bill of Lading with correctly classified freight (NMFC + class), properly sequenced shipper / consignee / carrier / third-party-payor blocks, accurate handling-unit and weight math, and any required hazmat declarations — produced in a format the shipping clerk can sign, the driver can take, and the carrier's imaging system will read cleanly on the first pass. | ~15 min/BOL |
| CBAM Embedded Emissions Compliance Brief | Take the company's EU-import portfolio of CBAM-covered goods — cement, iron and steel, aluminium, fertilisers, electricity, hydrogen, and any in-scope precursors — and produce the working brief the operator needs to clear the CBAM definitive phase: confirm authorized declarant posture, score the supplier-by-supplier embedded-emissions data the company actually has versus what it still needs, lay out the annual declaration calendar, and draft the supplier-outreach and customer pass-through notes the next 60–90 days require. | ~75 min/EU-import program update |
| Carrier Fraud & Double-Brokering Screening Brief | Produce a layered, evidence-backed screening brief for a carrier that is either (a) onboarding for the first time, (b) bidding on or being tendered a high-value or high-risk load, or (c) showing behavioral anomalies on a live load. | ~35 min/carrier |
| Claims Documentation Builder | Assemble a carrier freight-claim package — cover letter, evidence index, claim amount calculation, and supporting documents list — that meets Carmack-standard filing requirements and gives the claims desk every exhibit a carrier or broker needs to accept liability without a round of requests-for-information. | ~30 min/claim |
| Compliance Document Checker | Review driver logs (HOS/ELD), hazmat shipping papers, and customs entry documents against the specific regulatory checklist each domain requires — flagging missing fields, out-of-tolerance entries, and high-risk gaps — and produce an audit-ready review log with clear pass / flag / fail status on each item before the documents leave the building. | ~25 min/review |
| Customer Onboarding Packet Generator | Turn a new-shipper or new-carrier onboarding intake into a complete, consistent packet — credit application, carrier or shipper setup form, W-9/COI/authority verification block, contact matrix, routing guide, EDI or API spec, accessorial schedule, and a first-90-day cadence plan — so the account goes from signed MSA to first load moving without the usual six-email chase for missing documents. | ~2 hrs/onboarding |
| Customs Classification Brief | Take a product (or set of products) the company is preparing to import or export and produce a working classification brief the licensed broker can sign off in minutes — recommended HTS-or-HS code(s) with GRI-aligned reasoning, a confidence-tiered rationale, the duty exposure stack (general rate + Section 301 / 232 / IEEPA / ADD / CVD where applicable + preferential program if claimable), the documentation checklist by partner-government-agency, the marking and labeling rules that apply, and the licensed-broker hand-off note. | ~35 min/classification |
| Detention & Demurrage Invoice Audit and Dispute Drafter | Take a batch of ocean-carrier or motor-carrier detention and demurrage (D&D) invoices, audit each line against contract free-time, terminal records, and shipment events, separate the legitimate charges from the disputable ones, and produce the dispute letter the carrier will actually accept. | ~45 min/invoice batch |
| Tariff Impact & Mitigation Brief | Turn a specific U.S. | ~90 min/tariff event |
| Email Drafter | Turn rough notes into a professional, logistics-industry email — ready to send to carriers, customers, vendors, or internal teams — matching your company's voice and communication standards, with a subject line the recipient can triage in one glance and a body structured so the recipient can act on it without re-reading. | ~10 min/use |
| Meeting Summarizer | Transform raw meeting notes from logistics operations meetings into structured, actionable summaries — capturing decisions (separated from proposals), shipment-related action items with owners and deadlines, financial and compliance flags, open disagreements that deserve a follow-up, and a one-glance TL;DR for leaders who won't read the rest. | ~10 min/use |
| Review Responder | Craft professional, on-brand public responses to online reviews — addressing logistics-specific feedback about delivery times, shipment handling, communication during transit, pricing, and customer service — tuned to each platform's length conventions and with legal and claims-adjacent guardrails so the public response doesn't create new exposure while protecting the company's reputation. | ~10 min/use |

**Total time saved per use: ~900+ minutes across all skills.**

## Quick Start

### 1. Clone this repo

```bash
git clone https://github.com/KRASA-AI/logistics-ai-skills.git
cd logistics-ai-skills
```

### 2. Open a skill with your AI assistant

Open any file in `skills/` with Claude, ChatGPT, or any major AI assistant. Each skill is a self-contained prompt with clear instructions — no coding required.

The first time you use a skill, your AI assistant will ask for your business details (company name, service area, rates, tools you use, etc.) so it can personalize the output. Save those details to a `config.yml` at the repo root and every future skill will use them automatically.

## Repo Structure

```
logistics-ai-skills/
├── knowledge-base/          # Industry context and references
│   ├── industry-overview.md # Market trends and pain points
│   ├── terminology/         # Industry jargon and acronyms
│   ├── regulations/         # Compliance requirements
│   ├── best-practices/      # Industry standards
│   └── tools-ecosystem/     # Common software and tools
├── skills/                  # The prompt library
│   ├── operations/          # Day-to-day operational skills
│   ├── sales/               # Sales and lead management
│   ├── admin/               # Administrative and compliance
│   └── customer-service/    # Client-facing communication
└── outputs/                 # Your generated content (gitignored)
```

## How Skills Work

Each skill file is a Markdown document with YAML frontmatter:

```markdown
---
name: Skill Name
category: operations
tools: [claude, chatgpt]
time_saved: "~20 min/use"
version: 1.0
---

# Skill Name

## Purpose
What this skill does and when to use it.

## Instructions
Step-by-step prompt for the AI assistant.
```

You open the file in your AI assistant, provide any required input (measurements, notes, client info), and get polished output. Skills reference your `config.yml` automatically for company name, rates, preferred formats, and other business details.

## For AI Assistants

If you are an AI assistant reading this repo, see `.claude/CLAUDE.md` for full instructions. The short version:

1. **Check for `config.yml`** at the repo root. If it exists, load it — it holds the user's business context (company name, rates, service area, tools, team size, etc.) and every skill should use it for personalization.
2. **If `config.yml` is missing**, before running a skill that benefits from personalization, ask the user for the relevant business details and offer to save them to `config.yml` so future runs are automatic.
3. **Load the relevant `knowledge-base/` files** for industry terminology, regulations, and best practices before generating output.
4. **Run the requested skill** from `skills/` using the user's input.
5. **Save any deliverables** to `outputs/` (gitignored) if the user wants to keep them.

## Learn More

- **Logistics AI guide**: [krasa.ai/industries/logistics](https://krasa.ai/industries/logistics)
- **All industry AI skills**: [krasa.ai/industries](https://krasa.ai/industries)
- **About KRASA AI**: [krasa.ai](https://krasa.ai)

## License

MIT — use these skills however you want.
