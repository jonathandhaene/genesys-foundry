# Scenario: Real-Time Agent Assistance

## Overview

Agents receive AI-powered suggestions, knowledge snippets, and next-best-action recommendations **in real time during live customer interactions** — across voice, chat, email, and messaging channels.

The AI never responds to the customer directly. It acts as a **silent, intelligent copilot** visible only to the agent.

---

## Problem It Solves

| Problem | Without AI | With Contact Center Foundry |
|---|---|---|
| Finding the right answer | Agent searches 3–5 systems manually | AI surfaces top answer instantly |
| Inconsistent responses | Each agent finds different information | All agents see the same grounded answer |
| Long handle times | Manual lookup extends calls | Reduced search time cuts AHT |
| After-call work | Agent manually writes case notes | AI generates structured notes in seconds |
| Escalation uncertainty | Agent unsure when to escalate | AI suggests escalation trigger and path |

---

## How It Works

### 1. Interaction Starts
- Customer contacts (voice, chat, email, etc.)
- Genesys routes interaction to available agent
- Platform receives interaction start event
- Customer context loaded from CRM (name, account, open cases, history)
- Agent Companion panel shows customer context before first word is spoken

### 2. During the Interaction
- Voice: near-real-time transcription feeds intent detection
- Chat/Email: each customer message analysed on receipt
- Platform detects customer intent (e.g., "billing dispute", "technical issue", "cancellation")
- Relevant knowledge articles retrieved and AI response generated
- Suggestions appear in Agent Companion panel within 2–3 seconds

### 3. Next-Best-Action
- Based on intent, customer profile, and interaction history, AI suggests the most likely resolution path
- Examples:
  - "Customer is eligible for a goodwill credit — offer via case type X"
  - "This issue matches known defect KBA-1234 — escalate to Tier 2"
  - "SLA breach risk: customer has 2-hour SLA, now 90 minutes in — escalate"

### 4. Interaction Ends
- AI generates structured interaction summary
- Case notes formatted for CRM entry (one-click push to Salesforce/D365/ServiceNow)
- Sentiment and outcome classification recorded for quality scoring

---

## Agent Companion UI

The Agent Companion is a lightweight browser panel (embeddable in any desktop):

```
┌─────────────────────────────────────────────┐
│  AGENT COMPANION                   [●] Live  │
├─────────────────────────────────────────────┤
│  Customer: Sarah Thompson          Premium  │
│  Account: Acme Corp                         │
│  Open Cases: 1 (Case #45231 - Billing)      │
│  Last Contact: 3 days ago (Chat - resolved) │
├─────────────────────────────────────────────┤
│  AI SUGGESTION                    87% conf  │
│                                             │
│  "Based on the billing dispute raised,      │
│   the customer is entitled to a refund      │
│   under clause 4.2 of the enterprise SLA.  │
│   Use form BL-204 to process."              │
│                                             │
│  Source: Enterprise SLA Policy v2.3         │
│  [👍 Helpful] [👎 Not Helpful] [Copy]       │
├─────────────────────────────────────────────┤
│  NEXT BEST ACTION                           │
│  → Offer SLA credit (£150)                  │
│  → Create case type: Billing Adjustment     │
└─────────────────────────────────────────────┘
```

---

## Supported Channels

| Channel | Real-Time Assist | Case Notes | CRM Writeback |
|---|---|---|---|
| Voice (Genesys) | ✅ (via transcription) | ✅ | ✅ |
| Chat (Genesys) | ✅ | ✅ | ✅ |
| Email | ✅ (on receipt) | ✅ | ✅ |
| WhatsApp / SMS | ✅ | ✅ | ✅ |
| Social (X, Facebook) | ✅ | ✅ | ✅ |

---

## Business Impact Metrics

| Metric | Typical Improvement |
|---|---|
| Average Handle Time (AHT) | -15 to -30% |
| After-Call Work (ACW) | -40 to -70% |
| First Contact Resolution (FCR) | +10 to +20% |
| Agent Satisfaction (ASAT) | +15 to +25 NPS points |
| Knowledge accuracy | +30% (grounded vs. ad hoc search) |

---

## CRM Compatibility

| CRM | Context Load | Case Notes Push | Next-Best-Action |
|---|---|---|---|
| Microsoft Dynamics 365 | ✅ | ✅ | ✅ |
| Salesforce | ✅ | ✅ | ✅ |
| ServiceNow | ✅ | ✅ | ✅ |
| Zendesk | ✅ | ✅ | Roadmap |
