# Platform Extensibility

## Extension Points

Contact Center Foundry is designed to be extended by enterprise teams and partners. The following extension points are supported:

---

## 1. Custom Channel Adapters

If your organisation uses a contact center or messaging platform not natively supported, you can implement a custom adapter.

### Adapter Contract

A channel adapter must:
1. Subscribe to events from the source system (polling or webhook)
2. Transform events into the **Unified Interaction Model (UIM)** format
3. Publish transformed events to the platform's Azure Service Bus topic

### Implementation

Custom adapters are implemented as Azure Functions (Durable or HTTP-triggered) and deployed to the platform's Azure subscription.

```python
# Example adapter skeleton (Python / Azure Functions)
import azure.functions as func
from contactcenter_foundry_sdk import UnifiedInteractionModel, InteractionEventPublisher

def main(req: func.HttpRequest) -> func.HttpResponse:
    raw_event = req.get_json()
    uim = UnifiedInteractionModel(
        interaction_id=raw_event["id"],
        channel="custom_chat",
        direction="inbound",
        participants=[...],
        transcript=[...]
    )
    InteractionEventPublisher.publish(uim)
    return func.HttpResponse(status_code=202)
```

---

## 2. Custom Knowledge Connectors

To index knowledge from a source not natively supported:

1. Implement the `IKnowledgeConnector` interface
2. Configure the connector in the Knowledge Service
3. Documents are automatically chunked, embedded, and indexed

### Connector Interface

```python
class IKnowledgeConnector:
    def list_documents(self, since: datetime) -> List[Document]:
        """Return documents modified since the given timestamp."""
        ...

    def get_document(self, document_id: str) -> Document:
        """Return a single document by ID."""
        ...

    def get_access_control(self, document_id: str) -> List[str]:
        """Return list of user/group IDs authorised to access this document."""
        ...
```

---

## 3. Custom Prompt Templates

The prompt engineering framework supports custom templates registered via the Prompt Registry API.

### Template Structure

```json
{
  "templateId": "custom-agent-assist-v1",
  "version": "1.0.0",
  "persona": "agent",
  "useCase": "product-specialist-assist",
  "systemPrompt": "You are a specialist assistant for...",
  "userPromptTemplate": "Customer question: {{query}}\n\nContext:\n{{context}}\n\nProvide a concise, accurate answer.",
  "variables": ["query", "context"],
  "maxTokens": 500,
  "temperature": 0.1,
  "governanceProfile": "standard"
}
```

Templates are version-controlled and require approval from the AI Platform Owner before activation in production.

---

## 4. Custom Quality Scoring Rubrics

Quality scoring rubrics can be customised per tenant or per agent group.

### Rubric Definition

```json
{
  "rubricId": "financial-services-v2",
  "dimensions": [
    {
      "name": "Regulatory Disclosure",
      "weight": 0.30,
      "evalMethod": "script_match",
      "config": { "requiredPhrases": ["past performance", "capital at risk"] }
    },
    {
      "name": "Customer Suitability Check",
      "weight": 0.25,
      "evalMethod": "llm_assess",
      "config": { "promptTemplate": "quality-suitability-check-v1" }
    }
  ]
}
```

---

## 5. Webhook Integrations (Outbound)

Platform events can be forwarded to external systems via configurable webhooks:

| Event | Example Use |
|---|---|
| `suggestion.generated` | Push suggestion to custom agent desktop |
| `flag.raised` | Trigger compliance workflow in external ITSM |
| `interaction.ended` | Update external reporting platform |
| `quality.scored` | Feed QA platform with AI scores |

Webhooks are configured per tenant in the administration portal and support:
- HMAC-SHA256 request signing
- Retry with exponential backoff
- Dead-letter queue for failed deliveries

---

## 6. SDK (Coming in Phase 2)

A TypeScript/Python SDK is planned for Phase 2 that will wrap the REST and event APIs for faster integration development.

Planned capabilities:
- Agent Companion UI widget (embeddable React component)
- Server-side SDK for adapter development
- CLI for prompt template management
- Infrastructure-as-Code modules for rapid deployment
