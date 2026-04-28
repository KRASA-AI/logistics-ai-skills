# Logistics Platform Security Controls — Best-Practices Reference

Short reference consumed by `skills/admin/carrier-fraud-screening-brief.md`, `skills/operations/pickup-identity-verification-brief.md`, `skills/operations/carrier-insider-risk-brief.md`, and the post-event review step of `skills/admin/claims-documentation-builder.md` when a compromise pattern is the suspected root cause. It describes the *internal* IT and identity controls a freight broker, 3PL, or carrier operations team should run against, and the runbook the team executes when a logistics-platform credential is suspected of being compromised. Not a substitute for a SOC 2 / ISO 27001 / CMMC compliance program, an MSSP engagement, or counsel.

## Why this reference exists

Through 2025 and into 2026, the cargo-theft attack surface visibly extended past paper carrier vetting and physical pickup verification into the dispatcher's logistics-platform credentials themselves. A 2026 disrupted phishing-as-a-service operation against major freight loadboard, fleet card, and freight-exchange platforms surfaced a five-month window over which roughly sixteen-hundred unique platform credential pairs were stolen, multi-factor codes were intercepted in real time through a chat-platform-driven console, and downstream double-brokering, freight impersonation, and mailbox-compromise activity was coordinated from the same infrastructure. The trade-press cycle through Q1 2026 reinforced the framing: cyberattacks against logistics targets are projected to roughly double year-over-year, and the impersonation-based theft pattern that the existing screening, pickup, and insider-risk briefs cover increasingly *originates* upstream of those briefs — at the moment a real broker's real account on a real platform begins issuing tenders the broker does not know about.

The repo's existing operations and admin briefs assume the company's own credentials are intact. This file is the reference for the controls that keep that assumption true, and for the runbook the team executes when it isn't.

The file is deliberately scoped to the *operator's* IT-and-identity surface — the broker's, 3PL's, or carrier ops team's own accounts and inboxes — not to the counterparty's. Counterparty-side risk continues to live in the existing `carrier-fraud-screening-brief.md` (paper layer), `pickup-identity-verification-brief.md` (gate), and `carrier-insider-risk-brief.md` (load-assignment).

## Control families

### Email authentication

- Publish SPF, DKIM, and DMARC records on every domain the company sends from, including marketing and ticketing subdomains. Aligned-from is what stops a spoofed-from-the-broker phishing email from passing inbox checks
- Move DMARC policy to **p=reject** rather than leaving it at p=none. p=none catches *nothing*; it just produces a report. The Diesel-Vortex-style operation depends on the ability to spoof a target's domain or a typosquat that looks close enough; a hard-fail policy raises that cost
- Ingest DMARC RUA / RUF reports into a tool the security or IT lead actually reads. The reports are how the team sees a spike in spoofed sends from infrastructure the company does not own
- SPF in **-all** (hard-fail) mode rather than ~all (soft-fail). DKIM signing on every active sender, not just the marketing platform
- Periodic review of the SPF include chain — every vendor that has been added to the chain over time stays there until removed. A long include chain quietly accumulates infrastructure the broker does not control

### Multi-factor authentication

- MFA on every login to every logistics platform the team uses (DAT, Truckstop, Highway, Sayari, Carrier411, Truckstop, RMIS, MyCarrierPackets, Highway Express, the company's TMS / WMS / ERP, the company's email itself, the company's fuel-card and quick-pay platforms, the broker's bank treasury portal, the company's CRM)
- *MFA alone is not enough against this attack pattern.* The 2026 phishing-as-a-service operation captured one-time-password and SMS codes in real time and replayed them inside the same session. **Phishing-resistant MFA** — FIDO2 hardware keys, device-bound passkeys, or platform-bound certificates — is the load-bearing control on high-value workflows (treasury / quick-pay, the dispatch inbox, TMS admin, the fleet-card console). On lower-value accounts, TOTP-with-app is acceptable; SMS is not
- Conditional access: device-managed-only on dispatch and treasury, geo-fenced on the platforms the team does not log into from outside the company's working geographies, session-bound certificates on platforms the carrier ecosystem supports (DAT and Highway have published guidance on this pattern). The conditional-access policy is what turns a stolen credential into a credential that does not work from the attacker's infrastructure

### Domain monitoring and typosquat detection

- The 2026 attack pattern depends on lookalike domains. The repo's prior monitor logs flagged the lowercase-i / uppercase-I trick (`tlmocom.com` for `timocom.com`, `dat-truckstop.com` for `dat.com`); Cyrillic homoglyph substitution in sender names (`а` Cyrillic looks identical to `a` Latin) was captured on the same operation
- Monitor for newly-registered lookalike domains against the company's own brand and against the loadboard / TMS / fleet-card platforms the team uses every day. Monitoring services exist; a weekly DMARC-report review is also a leading indicator (a typosquat domain rarely sets up a real DMARC record, so misalignment shows up in the report)
- DNS filtering at the network and endpoint layer to block known-typosquat domains. Browser-based protection (Safe Browsing, equivalent) catches the residual, but DNS is the better defensive layer for a workforce that mixes personal devices on the same network
- Treat *internal-looking* email — a "from" address that resembles a colleague's — with the same scrutiny as inbound mail. The 2026 operation routinely impersonated internal senders to redirect dispatch comms or change remit-to instructions

### Credential hygiene

- Password manager rolled out to every employee on every workstation that touches a logistics platform. The fundamental gain is per-platform unique credentials — credential stuffing against a re-used password is the first cheap try
- No shared credentials across platforms or across employees. Where shared accounts existed for legacy reasons (a generic `dispatch@` DAT login), break them up before the next compromise event forces it
- On-boarding and off-boarding tied to the directory the company uses (Okta, Entra, Workspace identity). When an employee leaves, the platforms they had access to are revoked the same day. Departed-employee accounts that stayed live were a recurring entry point in 2025 trade-press incident summaries
- Credential rotation on detection — *not* on calendar. Forced quarterly rotation drives password-recycling habits that work against the program. Rotation when an indicator fires (DMARC anomaly, account-lockout pattern, dark-web credential surfacing on a service the team uses) is the higher-leverage trigger

### Endpoint, session, and access controls

- Endpoint detection and response (EDR) on every workstation that logs into a logistics platform or the company's email. Antivirus alone does not catch the post-compromise tooling the 2026 operation used (remote-access trojans, Telegram-based session relays)
- Session timeout on the dispatch inbox, the TMS, and the fleet-card / quick-pay platforms. Idle-timeout is a low-cost control with high-value payoff: a stolen credential that requires re-auth every two hours is harder to monetize than one that stays alive for a week
- Block sessions on unmanaged devices for high-value workflows. The dispatch inbox accessed from a personal phone with no MDM is the single most common 2026 exposure vector after typosquat-phishing
- Restricted egress on TMS / treasury / fuel-card workstations — block outbound to known anonymous-proxy infrastructure and to chat-platform APIs that an unmanaged user does not need (Telegram bot APIs, in particular)
- Vendor-side controls the company can ask for: every loadboard, TMS, and fleet-card vendor in the team's stack should be asked, in writing, for their phishing-resistant-MFA option, their session-token revocation API, and their incident-notification SLA. Vendor pages are the source of truth on which platforms support which control; the conversation is the operator's responsibility

### Mailbox-compromise specific controls

- Mail-flow rules that the dispatch / billing inbox cannot externally forward without admin approval. The 2026 operation routinely set silent forwarding rules so the attacker received every inbound load tender, every quote, and every payment instruction without the operator noticing for weeks
- Periodic review of inbox rules across the team: detection is straightforward (the rule list is queryable in the email platform's admin console), but only if someone is looking
- Quarantine on inbound emails with newly-registered sender domains and on emails that rewrite the reply-to or remit-to. Both behaviors are leading indicators for the impersonation pattern
- Monitoring for mailbox-rule changes as a security event in the SIEM or audit log, not just as an admin convenience event

### Logistics-platform-specific controls (vendor-side)

- DAT, Truckstop, Highway, Sayari, Carrier411, RMIS, and the major TMS vendors have published 2026 guidance on session-bound MFA, account-impersonation reporting, and rapid-revocation. The company's IT lead should hold the latest version of each vendor's security configuration page in the runbook (not embedded here — the pages change)
- Where a vendor offers a *broker-verification* feature (a verification mark, a badge, an independent verification feed), the company's profile should be on the maximum-verification tier the vendor offers. The cost is low and the deterrent value against being chosen as an impersonation target is real
- When a vendor's incident affects the company (the 2026 phishing-as-a-service operation explicitly hit DAT, Penske, EFS, Timocom, Teleroute, and others), the operator's posture is to (1) rotate credentials on that platform, (2) enable any newly-shipped MFA tier the vendor pushes, and (3) capture the incident-response email-thread for the runbook archive

## Incident-response runbook

The runbook is the procedure the IT lead and the operations lead jointly run when a credential or mailbox is suspected of being compromised. It is short on purpose — long runbooks rot; short runbooks get used.

### Detection sources

- DMARC report anomaly (a sudden spike in spoofed-from-the-domain sends from infrastructure the company does not own)
- Login-anomaly review on the loadboard, TMS, treasury, or fleet-card platform (impossible-travel logins, login from a country the team does not work from, login at hours that do not match the normal work pattern)
- Carrier dispute of a load tender the broker did not knowingly send, or a payment direction the broker did not authorize
- Customer dispute of a remit-to or routing-instruction change the customer received from a sender that looked like the operator's email
- Vendor security advisory naming the operator's account in a disclosed incident
- An employee reporting a suspicious authentication prompt they did not initiate (the most common true-positive in 2026 trade-press incident summaries)

### First thirty minutes

1. **Force credential reset and revoke active sessions** on the affected account across every platform that supports session-token revocation
2. **Disable inbox forwarding rules** on the affected mailbox; capture the rule set as evidence before deleting it
3. **Notify the platform vendor's fraud-and-incident contact** — every major loadboard and fleet-card platform has one. Get the vendor's case number and the vendor's recommended next steps in writing
4. **Stop pending money movement** routed through the affected workflow until verified — quick-pay, factoring releases, fleet-card transactions, ACH on file
5. **Assemble the incident channel** — an ad-hoc channel that includes the IT lead, the operations lead, and (if money has moved) the CFO or finance lead. Outside counsel and the cyber-insurance carrier are added on confirmed-compromise rather than on suspicion
6. **Capture the timeline** — last known clean login, first suspected anomalous action, the volume and direction of any unauthorized activity. The timeline is what the cyber insurer and the platform vendor's investigator will ask for first

### First twenty-four hours

- Customer notification — *only* on confirmed unauthorized customer-facing activity. The operator's voice should be plain, factual, and quick: what the operator confirmed, what the operator has done, what the customer should do (if anything). Premature notification on a still-suspected compromise creates a second incident; delayed notification on a confirmed compromise is its own liability
- Carrier notification when a load tender or payment instruction was affected. The operator's voice on the carrier side is parallel to the customer voice — what was confirmed, what was reversed, what the carrier should treat as no-longer-valid
- Internal directive — a one-line instruction to the operations team that any inbound load, payment instruction, or contact-information change touching the affected accounts is held until the incident is closed
- Evidence preservation — mailbox snapshot, session log, audit log, vendor case correspondence. The single most common 2025–2026 incident-handling failure pattern is evidence rotated out of retention before the investigator has it
- Cyber-insurance notice. Every cyber policy has a notice clause; the clock on the notice clause runs from "knew or should have known," not from "fully understood"

### First week

- Root-cause confirmation — phishing email, lookalike domain, vendor-side breach, departed-employee credential, MFA bypass via real-time interception, malware on the workstation. The runbook is updated when the root cause is confirmed; the controls that did not catch the incident are revisited with a specific change rather than a generic "tighten security" item
- Lessons-learned note circulated to the team. The circulation is short — what happened, what was tried, what worked, what did not. Long postmortems are skipped by the team that needs to learn from them
- Outside reporting where applicable — FBI IC3, FMCSA's broker-fraud-and-identity-theft contact when a broker authority was used in the attack, NMFTA's reporting program for transportation-industry incidents. The customer's customer-data-protection regulator may also need a notice depending on the data classification

### Disclosure cadence

The company's disclosure obligations depend on its size, customer mix, jurisdiction, and the data classification involved. The runbook should encode the company's specific obligation set rather than copying the generic obligations here. The incident channel calls in counsel before any external statement is finalized.

## How the brief skills should use this reference

### `carrier-fraud-screening-brief.md`

When the screening surfaces a counterparty-side anomaly that could plausibly be a compromise rather than a routine mistake — a sudden change in dispatcher contact email or domain, a new remit-to that does not align with the registered authority, a load-tender pattern that is inconsistent with the carrier's historical behavior — the brief should add a one-line *Compromise-Possible* note that recommends parallel notification to the counterparty's IT-or-fraud contact rather than treating the anomaly as a unilateral fraud finding. The 2026 attack pattern routinely uses real, fully-vetted carrier and broker accounts whose owners are themselves victims; the screening brief should not treat a compromised counterparty as a hostile counterparty by default.

### `pickup-identity-verification-brief.md`

When the gate-side reconciliation surfaces a tendered-vs-arrived mismatch that aligns with email-redirect double-brokering — the load tender originated from an account that is now suspected of compromise, the arriving driver's paperwork chains to a different broker than the dispatch system shows — the pickup brief should run its disposition (Release / Conditional Release / Hold / Escalate) and trigger the IR runbook in parallel rather than holding pickup until IR concludes. The pickup decision is driver-and-tractor-and-paperwork-on-the-ground; IR is upstream and runs on its own clock.

### `carrier-insider-risk-brief.md`

When the load-assignment-side review surfaces a behavior pattern consistent with information leakage — the right driver appearing on the right load with timing that suggests advance knowledge — the brief should treat compromise of the broker's own load-tender system or dispatch inbox as one of the alternative explanations. The brief's existing four lenses (tenure / load-mix step-up / cluster / behavior pattern / controls) cover the carrier-insider hypothesis; the IT-side hypothesis is captured in the cluster lens when multiple loads show the pattern, and routes to the IR runbook rather than to the carrier's dispatcher.

### `claims-documentation-builder.md`

A claim that cites a settlement-paid-to-the-wrong-remit-to, or a load-receipt that disputes a tender the broker has on file, may be downstream evidence of a mailbox compromise rather than a counterparty fraud. The claims builder's existing internal-only Fraud-Indicator Review block should add a *Compromise-Possible* check that, when tripped, routes the claim to the IR runbook in parallel with the standard claims workflow.

## Scope limits

This reference is operator IT-and-identity best practices. It is not a substitute for:

- A SOC 2, ISO 27001, or CMMC compliance program, where the operator's customer base requires one
- An MSSP engagement on detection-and-response, on networks the company does not run itself
- Outside counsel or the cyber-insurance carrier on disclosure, regulatory notice, and breach litigation
- The vendor-platform-specific configuration pages on DAT, Truckstop, Highway, Sayari, Carrier411, the company's TMS, and the company's email platform — those pages are the source of truth on which controls each vendor offers
- A penetration test or red-team engagement on the company's actual posture

The runbook here is a *short, durable* version. The IT lead's company-specific runbook in the company's incident-response system is the operative document.

## Cross-links

- `skills/admin/carrier-fraud-screening-brief.md` — adds a Compromise-Possible note and routes to the IR runbook when a counterparty's anomaly could be a compromise
- `skills/operations/pickup-identity-verification-brief.md` — runs the IR runbook in parallel when gate-side mismatch aligns with email-redirect double-brokering
- `skills/operations/carrier-insider-risk-brief.md` — captures the IT-side hypothesis under the cluster lens and routes to the runbook when supported
- `skills/admin/claims-documentation-builder.md` — Compromise-Possible check on claims that suggest mailbox compromise; routes to the IR runbook in parallel with claims workflow
- `knowledge-base/best-practices/claims-fraud-indicators.md` — counterparty-side fraud indicators; this file is the operator-side parallel
- `knowledge-base/regulations/` — disclosure-and-notice obligations live there when the company's regulator-facing posture is captured

## Maintainer notes

- The vendor-specific controls (DAT, Truckstop, Highway, Sayari, Carrier411, TMS, fleet card) should be configured per the company's *actual* platform stack and captured in `config.yml` rather than embedded here. Vendor configuration pages change; this reference does not chase them
- Phishing-resistant MFA (FIDO2 / passkeys) is the load-bearing control given the 2026 attack pattern. When a new MFA bypass technique is documented in trade-press coverage, this file should be updated with a one-line note in the relevant control family rather than a wholesale rewrite
- The runbook is short on purpose. Resist the temptation to add every contingency the team can imagine; the runbook is the procedure the team executes under stress, and its value is in being short enough to be reliably followed
- Incident-disclosure obligations vary by company size, customer base, jurisdiction, and data classification. The company's specific obligation set belongs in the company's IR system and in counsel's playbook, not here
- Sibling-file backlog: `vendor-security-questionnaire.md` (the operator-side checklist for vetting a new TMS, loadboard, or fleet-card vendor's security posture); `data-classification-and-retention.md` (the operator-side reference on which data classes drive which controls). Build either when an operator workflow needs them
