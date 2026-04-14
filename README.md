# Logistics AI Skills

**Free, open-source AI prompts and workflows built for logistics teams.** Clone this repo, point your AI assistant at it, and start saving hours every week.

> Built and maintained by [KRASA AI](https://krasa.ai) — free AI tutorials and skills for every industry.
> See all industries at [krasa.ai/industries](https://krasa.ai/industries).

---

## What's Inside

This repo is a complete AI toolkit for logistics. Every skill is a standalone prompt file that works with **Claude, ChatGPT, or any major AI assistant** — no coding required.

| Skill | What it does | Time saved |
|-------|-------------|------------|
| Carrier Performance Scorecard | Turn a pile of historical shipment data into a clean, decision-ready carrier scorecard that grades each carrier on on-time performance, damage/claims rate, invoice accuracy, tender acceptance, responsiveness, and cost competitiveness — then recommends lane-level "preferred / backup / probation" tiers so procurement can reshape the routing guide with confidence. | ~45 min/scorecard |
| Carrier Rate Comparison | Summarize rate quotes across carriers with transit times, accessorials, and trade-offs. | ~15 min/comparison |
| Dock Scheduling & Detention Prevention Brief | Analyze dock appointment data, carrier arrival patterns, and detention/demurrage invoices to produce an action-ready brief that reshapes the daily dock schedule, cuts avoidable detention charges, and protects customer-critical inbound/outbound flows. | ~30 min/brief |
| Route Optimization Brief | Take a raw stop list for a multi-stop P&D or delivery route and produce a resequenced, driver-ready plan with defensible mileage and time savings, HOS-feasibility checks, and a ready-to-send dispatch communication — so the planner can commit the new sequence in minutes instead of eyeballing a map. | ~30 min/route |
| Shipment Exception Handler | Triage shipment exceptions (delays, damages, refused deliveries, weather holds, customs holds) and produce a structured response plan with root-cause analysis, corrective actions, and ready-to-send stakeholder communications. | ~20 min/exception |
| Shipment Status Summarizer | Compile tracking data into a client-ready status update with ETAs and exceptions. | ~10 min/update |
| Freight Quote Response Drafter | Turn internal rate data and shipment requirements into a professional, customer-ready freight quote response — complete with pricing breakdown, transit time, terms and conditions, and value-add highlights that differentiate your company from competitors. | ~15 min/quote |
| Load Tender Response Drafter | Evaluate an inbound load tender against carrier or broker acceptance criteria and produce a structured accept / counter / reject recommendation — along with the customer-facing response (EDI 990 equivalent language or email reply) and an internal decision rationale that a dispatcher or sales rep can approve in under a minute. | ~10 min/tender |
| Shipment Inquiry Responder | Draft professional, empathetic customer responses to common shipping inquiries — tracking questions, delay explanations, delivery confirmations, damage reports, and rate requests — so your team can respond faster and more consistently. | ~8 min/inquiry |
| BOL Generator | Create a bill of lading from shipment details with proper commodity descriptions. | ~10 min/BOL |
| Claims Documentation Builder | Assemble a carrier freight-claim package — cover letter, evidence index, claim amount calculation, and supporting documents list — that meets Carmack-standard filing requirements and gives the claims desk every exhibit a carrier or broker needs to accept liability without a round of requests-for-information. | ~30 min/claim |
| Compliance Document Checker | Review driver logs (HOS/ELD), hazmat shipping papers, and customs entry documents against the specific regulatory checklist each domain requires — flagging missing fields, out-of-tolerance entries, and high-risk gaps — and produce an audit-ready review log with clear pass / flag / fail status on each item before the documents leave the building. | ~25 min/review |
| Customs Classification Brief | Analyze product descriptions and generate a customs classification brief with recommended HTS/HS codes, duty rate estimates, required documentation checklist, and flagged compliance risks — giving your team a head start on clearance preparation before submission to a licensed customs broker. | ~25 min/classification |
| Email Drafter | Turn rough notes into a professional, logistics-industry email — ready to send to carriers, customers, vendors, or internal teams — matching your company's voice and communication standards. | ~10 min/use |
| Meeting Summarizer | Transform raw meeting notes from logistics operations meetings into structured, actionable summaries — capturing decisions, shipment-related action items, carrier performance notes, and follow-ups so nothing falls through the cracks. | ~10 min/use |
| Review Responder | Craft professional, on-brand responses to online reviews — addressing logistics-specific feedback about delivery times, shipment handling, communication during transit, pricing, and customer service — so your team can respond quickly and consistently across all review platforms. | ~10 min/use |

**Total time saved per use: ~303+ minutes across all skills.**

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
