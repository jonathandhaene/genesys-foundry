# Scenario: Multi-Channel Customer Intelligence

## Overview

Modern customers interact across many channels — often switching mid-journey. A customer may start with a web chat, send a follow-up email, and then call. Without a unified view, each agent sees a fragment of the customer story.

Genesys Surround creates a **unified customer intelligence profile** that aggregates signals across all channels and CRM systems, providing agents and supervisors with a complete, real-time view of every customer relationship.

---

## The Problem: Channel Fragmentation

```
Customer Journey (Fragmented State)
                                       ┌──────────────────────────────┐
Monday: Web chat ──────────────────────► Chat Agent A                 │
        "Can't log in"                  Sees: only this chat session   │
                                       └──────────────────────────────┘

Tuesday: Email ────────────────────────► Email Queue B                │
         "Still broken, very unhappy"   Sees: only this email         │
                                       └──────────────────────────────┘

Wednesday: Phone call ─────────────────► Voice Agent C               │
           "I want to cancel"           Sees: only this call          │
                                       └──────────────────────────────┘
```

Agent C has no context. The customer must repeat themselves. Frustration escalates.

---

## The Solution: Unified Customer Intelligence

```
Customer Journey (With Genesys Surround)
                                       ┌──────────────────────────────────┐
Monday: Web chat                        Agent A sees:                     │
Tuesday: Email              ──────────► - Full interaction history         │
Wednesday: Phone call                   - Unified sentiment trend          │
                                        - Open issue context               │
                                        - Risk: cancellation likely         │
                                        - Recommended action: retention     │
                                       └──────────────────────────────────┘
```

---

## Unified Customer Intelligence Profile

At the start of any interaction, the platform assembles a real-time customer intelligence profile:

```json
{
  "customerId": "cust-789",
  "name": "Sarah Thompson",
  "account": "Acme Corp",
  "tier": "Enterprise",
  "contactHistory": {
    "totalInteractions": 14,
    "last30Days": 3,
    "channels": ["voice", "chat", "email"],
    "avgSentiment": "neutral",
    "sentimentTrend": "declining"
  },
  "openIssues": [
    {
      "caseId": "CASE-45231",
      "channel": "email",
      "createdAt": "2025-10-13",
      "topic": "Login access issue",
      "status": "open",
      "ageDays": 3,
      "slaStatus": "at_risk"
    }
  ],
  "churnRisk": {
    "score": 0.72,
    "signals": ["multiple contacts", "declining sentiment", "long-running open case"],
    "recommendation": "priority_retention"
  },
  "crm": {
    "source": "salesforce",
    "accountHealth": "yellow",
    "renewalDate": "2026-01-15",
    "contractValue": 85000
  }
}
```

---

## Channel Intelligence Features

### Cross-Channel Sentiment Tracking
- Sentiment analysed at every interaction turn, across every channel
- Aggregated sentiment trend shown to agent and supervisor
- Declining sentiment triggers proactive alerts

### Topic Continuity
- Topics from previous interactions are detected and flagged as context for the current interaction
- "This customer mentioned login issues 3 days ago — this call may be related"

### Channel Handoff Context
- When a customer switches channels, full context travels with them
- No repeated information required from the customer

### Interaction History Search
- Agents can search all previous interactions ("What was discussed about the login issue on Monday?")
- AI provides a summary answer without requiring the agent to scroll through transcripts

---

## Operational Intelligence (Ops/BI)

Beyond the agent and supervisor view, cross-channel intelligence powers operational analytics:

| Report | Description |
|---|---|
| Channel deflection analysis | How many issues could have been resolved on a lower-cost channel? |
| Topic heat map | What topics are driving the most contacts across all channels? |
| Cross-channel journey map | What is the most common channel sequence before resolution? |
| Sentiment funnel | At which channel and interaction number does sentiment typically decline? |
| Churn early warning | Which accounts show multi-signal churn risk? |

These reports are available via the platform's analytics API or through pre-built Power BI connectors.

---

## CRM Integration for Unified View

The platform aggregates customer data from all connected CRMs in real time:

| CRM | Data Retrieved | Update Frequency |
|---|---|---|
| Dynamics 365 | Account, cases, contacts, SLA | On interaction start |
| Salesforce | Account, cases, opportunities, health | On interaction start |
| ServiceNow | Incidents, requests, CMDB | On interaction start |
| Zendesk | Tickets, user profile, satisfaction | On interaction start |

When a customer interacts across systems (e.g., has a Salesforce account and a ServiceNow IT incident), the platform correlates the records by email/phone match and presents a unified view.
