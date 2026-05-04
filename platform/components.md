# Core Platform Components

## 1. Agent Copilot Service

### Responsibility
Provides real-time AI assistance to contact center agents during live customer interactions.

### Key Functions
- Subscribes to interaction events (new interaction, new transcript segment, transfer, end)
- Loads customer context from CRM via Integration Gateway
- Triggers intent detection and entity extraction on incoming customer messages
- Generates AI suggestions (answers, next-best-actions, knowledge snippets)
- Pushes suggestions to Agent Companion UI via WebSocket

### Configuration Points
- Persona-specific prompt selection (agent type, CRM system, industry)
- Confidence threshold for surfacing suggestions
- Knowledge sources authorised for this agent group
- Real-time assist vs. manual-trigger mode

---

## 2. Knowledge Service

### Responsibility
Provides unified, grounded knowledge retrieval across all indexed enterprise sources.

### Key Functions
- Accepts natural language queries with optional metadata filters
- Executes hybrid search (vector + BM25) against Azure AI Search
- Re-ranks results using cross-encoder model
- Invokes GPT-4o with retrieved context to generate a grounded answer
- Returns answer with source citations and confidence score

### API

```
POST /api/v1/knowledge/query
{
  "query": "What is the refund policy for enterprise contracts?",
  "tenantId": "...",
  "userId": "...",
  "filters": {
    "source": ["servicenow", "sharepoint"],
    "language": "en"
  },
  "maxSources": 5
}
```

**Response**:
```json
{
  "answer": "Enterprise contracts are eligible for...",
  "confidence": 0.87,
  "sources": [
    { "title": "Enterprise Refund Policy v2.3", "url": "...", "excerpt": "..." }
  ],
  "grounded": true
}
```

---

## 3. Summarisation Service

### Responsibility
Generates structured summaries of interactions at multiple stages: mid-interaction, post-interaction, and batch (e.g., shift summaries for supervisors).

### Summary Types

| Type | Trigger | Output |
|---|---|---|
| **Interaction Summary** | End of interaction | 3–5 sentence structured summary + key actions |
| **Case Notes** | End of interaction | Formatted case note ready for CRM entry |
| **Mid-Interaction Brief** | On-demand during interaction | Status summary for supervisor or transfer recipient |
| **Shift Brief** | Scheduled (start of shift) | Team performance highlights for supervisor |
| **Escalation Context** | Transfer event | Full context for receiving agent/supervisor |

### Output Format (Interaction Summary)

```json
{
  "interactionId": "...",
  "summary": "Customer called regarding delayed shipment on order #12345...",
  "keyPoints": [
    "Order placed 2 weeks ago, expected delivery overdue by 5 days",
    "Customer has premium SLA, entitled to expedited resolution",
    "Agent escalated to logistics team; ticket #LG-789 created"
  ],
  "sentiment": "frustrated → satisfied",
  "resolutionStatus": "resolved",
  "nextActions": ["Follow up within 24 hours", "Confirm shipment tracking update sent"]
}
```

---

## 4. Quality Intelligence Service

### Responsibility
Automates quality scoring of interactions against configurable rubrics, enabling 100% coverage instead of manual sampling.

### Scoring Rubric (configurable)

| Dimension | Weight | AI Assessment |
|---|---|---|
| Greeting and introduction | 10% | Script adherence check |
| Active listening signals | 15% | Transcript analysis |
| Accuracy of information | 25% | Knowledge grounding check |
| Empathy and tone | 20% | Sentiment and language analysis |
| Resolution effectiveness | 20% | Outcome classification |
| Compliance adherence | 10% | Compliance rule engine check |

### Compliance Flags
Automatically flagged patterns:
- PCI-DSS: credit card numbers spoken/typed during interaction
- GDPR: data deletion requests not properly acknowledged
- FSI: non-compliant advice language patterns
- Scripts: mandatory disclosure statements not delivered

---

## 5. Supervisor Intelligence Service

### Responsibility
Provides supervisors with real-time and retrospective intelligence about their team's performance and interaction quality.

### Features
- **Live Queue Monitor**: real-time sentiment and risk scores across active interactions
- **Intervention Alerts**: triggered when sentiment drops below threshold or compliance flag detected
- **Coaching Dashboard**: agent performance trends, knowledge gap indicators
- **Team Shift Brief**: AI-generated summary of previous shift performance for incoming supervisor
- **Escalation Context**: instant AI-generated context summary when escalation arrives

---

## 6. Knowledge Curation Service

### Responsibility
Supports knowledge managers in maintaining and improving the enterprise knowledge base using AI-driven insights.

### Features

| Feature | Description |
|---|---|
| **Gap Detection** | Identifies queries that returned low-confidence or no answers |
| **Content Quality Scoring** | Evaluates articles for clarity, completeness, and freshness |
| **Usage Analytics** | Shows which articles are retrieved most, and which are never used |
| **Staleness Detection** | Flags articles not updated in configurable period |
| **Conflict Detection** | Identifies contradictory information across sources |
| **Draft Assist** | AI-assisted drafting for new KB articles based on gap queries |

---

## 7. Integration Gateway

### Responsibility
Provides a normalised interface for all external system connectivity. Isolates the platform from source system API changes.

### Functions
- **Adapter management**: configures and manages connections to each external system
- **Event normalisation**: translates system-specific events into Unified Interaction Model
- **CRM data retrieval**: on-demand pull of customer, case, and account data
- **Write-back**: pushes AI-generated summaries and case notes back to CRM
- **Health monitoring**: tracks connector availability and latency

### Supported Connectors

| Connector | Read | Write | Events |
|---|---|---|---|
| Genesys Cloud | ✅ | ✅ | ✅ |
| Genesys Engage | ✅ | ❌ | ✅ |
| Microsoft Dynamics 365 | ✅ | ✅ | ✅ |
| Salesforce | ✅ | ✅ | ✅ |
| ServiceNow | ✅ | ✅ | ✅ |
| Zendesk | ✅ | ✅ | ✅ |
| SharePoint / Teams | ✅ | ❌ | ✅ |
