# Platform Layers – Deep Dive

## Layer 1: Interaction Layer

### Purpose
The Interaction Layer is the boundary between the external world and the Genesys Surround platform. It captures, normalises, and routes all interaction signals.

### Components

#### Channel Adapters
Each communication channel has a dedicated adapter responsible for:
- Receiving raw events from the source system (Genesys, chat platform, email gateway, etc.)
- Normalising them into the **Unified Interaction Model (UIM)**
- Routing them to the Orchestration Layer via Azure Service Bus

| Adapter | Source System | Protocol |
|---|---|---|
| Voice Adapter | Genesys Cloud / Engage | Genesys SDK / REST Events |
| Chat Adapter | Genesys, Teams, Web Widget | WebSocket / REST |
| Email Adapter | Exchange Online, SendGrid | SMTP / Graph API |
| Messaging Adapter | WhatsApp, SMS, Social | Channel-specific APIs |
| CRM Event Adapter | D365, Salesforce, ServiceNow, Zendesk | Webhooks / Change Data Capture |

#### Unified Interaction Model (UIM)

Every interaction — regardless of channel — is normalised into the following canonical structure:

```json
{
  "interactionId": "uuid",
  "tenantId": "uuid",
  "channel": "voice | chat | email | messaging | social",
  "direction": "inbound | outbound",
  "participants": [
    { "role": "customer", "id": "...", "channel_id": "..." },
    { "role": "agent", "id": "...", "crm_id": "..." }
  ],
  "context": {
    "crmCaseId": "...",
    "crmCustomerId": "...",
    "queue": "...",
    "skill": "..."
  },
  "transcript": [...],
  "metadata": { "startTime": "ISO8601", "language": "en-GB" }
}
```

---

## Layer 2: Orchestration Layer

### Purpose
The Orchestration Layer is the cognitive engine of the platform. It receives normalised interaction events and coordinates AI capabilities in response.

### Components

#### Agent Copilot Orchestrator
The primary service responsible for real-time agent assistance:
- Subscribes to interaction events from the Interaction Layer
- Maintains session state throughout an interaction lifecycle
- Triggers knowledge retrieval, summarisation, and next-best-action workflows
- Pushes AI-generated content to the Agent Companion UI

#### Workflow Engine
Long-running, stateful workflows coordinated via Azure Durable Functions:
- **Interaction Analysis Workflow** – transcription, intent detection, entity extraction
- **Knowledge Retrieval Workflow** – semantic search, re-ranking, response generation
- **Summarisation Workflow** – end-of-interaction summary, case note generation
- **Quality Scoring Workflow** – post-interaction evaluation against quality rubric

#### Event Router
Pub/sub event routing layer built on Azure Service Bus:
- Routes events to appropriate processing pipelines based on type and metadata
- Supports filtering, dead-letter handling, and retry policies
- Decouples producers from consumers for loose coupling

#### Session Context Manager
Maintains the state of each active interaction:
- Stores conversation turns, retrieved knowledge, and agent actions
- Provides context enrichment for each new AI request within a session
- Supports context handoff during transfers (agent to agent, channel switching)

---

## Layer 3: Knowledge Layer

### Purpose
Provides grounded, authoritative knowledge to all AI capabilities. Without this layer, AI responses rely solely on model training data — which is neither enterprise-specific nor trustworthy for compliance purposes.

### Components

#### RAG Engine (Retrieval-Augmented Generation)
- Converts each AI request into a semantic search query
- Retrieves top-N relevant document chunks from the Semantic Index
- Injects retrieved context into the LLM prompt
- Returns a grounded response with source attribution

#### Semantic Index
Built on Azure AI Search with hybrid search (keyword + vector):
- Indexes content from all enterprise knowledge sources
- Maintains freshness via incremental indexing pipelines
- Applies access control filters at query time (user permissions respected)

**Indexed Sources:**

| Source | Connector | Refresh Frequency |
|---|---|---|
| SharePoint / Teams | Microsoft Graph API | Incremental, near real-time |
| ServiceNow KB | ServiceNow REST API | Scheduled, configurable |
| Salesforce KB | Salesforce REST API | Scheduled, configurable |
| Dynamics 365 KB | D365 OData API | Scheduled, configurable |
| Zendesk Help Center | Zendesk API | Scheduled, configurable |
| Internal File Shares | Azure File Sync | Scheduled |
| PDF / Office Docs | Azure AI Document Intelligence | On-demand + scheduled |

#### CRM Data Connector
Provides real-time customer context at the start of each interaction:
- Customer profile, account, and tier
- Case history and open cases
- Last interaction summary
- Contract and entitlement status

#### Policy & Procedure Store
Version-controlled repository of internal procedures:
- Accessible only by authorised AI personas (agent copilot, compliance assistant)
- Indexed separately from public KB for access control granularity
- Change-controlled: each version is tracked and auditable

---

## Layer 4: AI Layer

### Purpose
Houses the large language models, embedding models, and the prompt engineering framework. This is the reasoning core of the platform.

### Components

#### LLM Foundation
- **Primary model**: Azure OpenAI GPT-4o
- Deployed in the customer's Azure subscription
- No data leaves the customer's Azure tenant
- Model access governed by Azure RBAC and private endpoints

#### Embedding Model
- **Model**: text-embedding-3-large
- Used for all semantic search operations in the Knowledge Layer
- Embeddings stored in Azure AI Search vector index

#### Prompt Engineering Framework
A governed, versioned library of prompt templates:
- Each template is tied to a specific use case and persona
- Templates are versioned in source control
- Deployed via CI/CD pipeline (not ad hoc)
- Performance metrics tracked per template

**Template categories:**

| Category | Examples |
|---|---|
| Agent Assistance | Real-time suggestion, next-best-action, knowledge retrieval |
| Summarisation | Interaction summary, case notes, shift briefing |
| Quality & Compliance | Interaction scoring, compliance flag detection |
| Supervisor | Team performance brief, coaching recommendations |
| Knowledge Management | Content gap detection, article quality scoring |

#### Response Validation
Every AI response passes through a validation pipeline before delivery:
- **Grounding check**: response must be traceable to retrieved context
- **Content safety**: Azure AI Content Safety filtering
- **Length and format**: enforced response structure
- **Confidence threshold**: low-confidence responses flagged for human review

---

## Layer 5: Governance Layer

### Purpose
Enforces security, privacy, compliance, and responsible AI across every platform operation. Not a separate system — governance controls are embedded in every layer.

### Components

#### Identity & Access Management
- Integrated with Microsoft Entra ID
- Role-based access control (RBAC) for every platform function
- Service-to-service authentication via Managed Identities (no secrets)
- Just-in-time elevated access for administrative operations

#### Audit Logging
- Every AI inference, knowledge retrieval, and data access logged
- Logs written to Azure Blob Storage with immutability policies
- Queryable via Azure Log Analytics
- Exportable for regulatory audit requests

#### PII Detection & Masking
- Pre-processing step applied before any data reaches the AI Layer
- Detects names, email addresses, phone numbers, account numbers, health information
- Applies masking or tokenisation based on configurable policy
- Preserves semantic meaning while protecting identity

#### Compliance Engine
- Configurable compliance profiles: GDPR, HIPAA, PCI-DSS, FSI
- Rule-based flagging for interaction content
- Automated alerts for compliance triggers
- Integration with Microsoft Purview for data governance

#### Model Monitoring
- Production model behaviour monitored against approved baselines
- Drift detection alerts when response quality degrades
- Human-in-the-loop escalation paths for edge cases
- Incident response playbook for model-related events
