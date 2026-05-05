# Personas & AI Journeys

Contact Center Foundry is designed for every role in the contact center — not just agents. Each persona has specific needs, pain points, and AI-assisted workflows.

---

## Persona Map

```
                        ┌─────────────────────┐
                        │   Contact Center     │
                        │     Operations       │
                        └──────────┬──────────┘
                 ┌─────────────────┼─────────────────┐
                 │                 │                 │
         ┌───────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐
         │    Agents     │  │ Supervisors │  │  Knowledge  │
         │               │  │             │  │  Managers   │
         └───────┬───────┘  └──────┬──────┘  └──────┬──────┘
                 │                 │                 │
         ┌───────▼───────────────────────────────────▼──────┐
         │              Operations & Analytics               │
         │          (Workforce Mgmt, Quality, BI)            │
         └───────────────────────────────────────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │    C-Level / DPO  │
                    │    CISO / Finance │
                    └───────────────────┘
```

---

## 1. Contact Center Agent

### Profile
Front-line representative handling customer interactions across voice, chat, email, and messaging. Under pressure to resolve quickly, accurately, and compliantly.

### Pain Points
- Searching multiple systems simultaneously during live calls
- Inconsistent answers depending on which KB is used
- Manual after-call work (summarisation, case logging)
- Escalation uncertainty — not knowing when or to whom

### AI-Assisted Journey

| Moment | AI Capability | Benefit |
|---|---|---|
| Call/chat starts | Context loaded from CRM automatically | No manual lookup needed |
| Customer explains issue | Real-time transcription + intent detection | Agent sees structured summary |
| Agent needs info | AI suggests relevant KB articles | Faster, more accurate answers |
| Complex issue | Next-best-action recommendation | Guided resolution path |
| Compliance-sensitive topic | Real-time script guidance | Reduced compliance risk |
| Interaction ends | Auto-generated summary + case notes | Seconds vs. minutes for ACW |

---

## 2. Supervisor / Team Lead

### Profile
Manages a team of agents, monitors live interactions, coaches staff, and ensures SLA compliance and quality standards.

### Pain Points
- Can only monitor a fraction of interactions manually
- Identifying coaching opportunities is reactive, not proactive
- Escalations land without context
- No real-time view of team knowledge gaps

### AI-Assisted Journey

| Moment | AI Capability | Benefit |
|---|---|---|
| Start of shift | AI-powered team performance brief | Instant situational awareness |
| Monitoring live queue | Sentiment and risk alerts on interactions | Proactive intervention |
| Escalation arrives | Full AI-generated context summary | No need to re-read thread |
| Post-interaction review | Auto-scored quality against rubric | Scalable QA across all interactions |
| Coaching session | AI-identified patterns and recommendations | Evidence-based coaching |
| End of day | Performance trend summary across team | Data-driven decisions |

---

## 3. Knowledge Manager

### Profile
Responsible for the accuracy, completeness, and accessibility of enterprise knowledge used by agents and AI systems.

### Pain Points
- Knowledge spread across 5+ systems (SharePoint, ServiceNow KB, CRM, wikis)
- No visibility into what knowledge agents actually use (or fail to find)
- Content goes stale without feedback loops
- No way to know if AI is using outdated information

### AI-Assisted Journey

| Moment | AI Capability | Benefit |
|---|---|---|
| Content audit | AI identifies stale, conflicting, or low-quality articles | Prioritised curation backlog |
| Content creation | AI-assisted drafting from raw materials | Faster, more consistent output |
| Gap detection | AI surfaces queries that found no good answer | Identifies missing knowledge |
| Search analytics | AI usage logs show what agents search and whether they find it | Demand-driven KB management |
| Publish cycle | AI validates coverage and flags compliance-sensitive content | Quality gate before publish |

---

## 4. Operations / Workforce Management

### Profile
Responsible for staffing, routing, capacity planning, and operational efficiency across the contact center.

### Pain Points
- Volume forecasting relies on historical data only
- Routing decisions don't incorporate real-time complexity signals
- No easy way to understand interaction complexity distribution

### AI-Assisted Journey

| Moment | AI Capability | Benefit |
|---|---|---|
| Capacity planning | AI-enhanced demand forecasting | More accurate staffing models |
| Routing rules | AI complexity scoring per interaction type | Intelligent skills-based routing |
| Real-time adjustment | AI-triggered staffing alerts during spikes | Dynamic rebalancing |
| Post-day analysis | AI-generated operational summary | Trend identification without BI overhead |

---

## 5. Quality Assurance / Compliance Officer

### Profile
Reviews interactions for compliance, quality, and risk. Responsible for regulatory reporting and audit readiness.

### Pain Points
- Can only review a small sample of interactions
- Manual review is slow, costly, and inconsistent
- Compliance breaches may not surface until after the fact

### AI-Assisted Journey

| Moment | AI Capability | Benefit |
|---|---|---|
| Interaction review | AI pre-scores all interactions against quality rubric | 100% coverage vs. sample |
| Risk detection | Flags potential compliance issues (PCI, GDPR, sensitive topics) | Proactive risk management |
| Audit preparation | Generates audit trail with AI inference logs | Audit-ready at any time |
| Trend reporting | AI-summarised quality trends by agent, team, topic | Executive-ready reporting |

---

## 6. CISO / DPO

### Profile
Responsible for data security, privacy compliance (GDPR, HIPAA), and AI governance. Sceptical of AI without evidence of controls.

### Key Concerns
- What data does the AI see? Who can access it?
- Are personal data and conversation content retained?
- Can the organisation prove what the AI said and why?
- How is model behaviour monitored and controlled?

### Platform Assurances

| Concern | Platform Response |
|---|---|
| Data access | Role-based, tenant-scoped, least privilege enforced |
| Data retention | Configurable retention policies; no default persistence of PII |
| Explainability | Every AI response logged with prompt, context, and model version |
| Model governance | Approved model registry; no unapproved model deployment |
| Compliance | Pre-built compliance profiles for GDPR, HIPAA, PCI-DSS |

> See [`/governance`](../governance/) for the full governance documentation.

---

## 7. CIO / CTO / Executive Sponsor

### Profile
Sets AI strategy, controls investment, and is accountable for outcomes. Needs strategic clarity, not product demos.

### Key Questions
- Does this require replacing our contact center platform?
- What is the ROI and how quickly can we see it?
- How does this scale as we add more systems?
- Who owns this platform long-term?

### Platform Value Proposition

| Question | Answer |
|---|---|
| Replace our platform? | No. Contact Center Foundry wraps around existing systems. |
| ROI timeline | Measurable efficiency gains within 60–90 days of Phase 1 |
| Scalability | Each new CRM or channel integrated via standard adapters |
| Ownership | Platform managed by your enterprise AI/cloud team |

> See [`/business`](../business/) for the full business case and ROI model.
