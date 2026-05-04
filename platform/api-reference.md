# API Design Patterns

## Design Principles

All Genesys Surround APIs follow these principles:

- **REST over HTTP/HTTPS** for synchronous request-response
- **AsyncAPI / Azure Service Bus** for event-driven asynchronous patterns
- **OpenAPI 3.1** specification for all REST APIs
- **OAuth 2.0 / OIDC** (via Microsoft Entra ID) for all authentication
- **Versioned** (`/api/v1/`, `/api/v2/`) — backwards-compatible minor versions; major version breaks communicated with deprecation notice
- **Tenant-scoped** — every request includes `tenantId` validated against the bearer token
- **Consistent error format** — RFC 7807 Problem Details

---

## Authentication

All API calls require a valid bearer token from Microsoft Entra ID.

```
Authorization: Bearer <access_token>
X-Tenant-Id: <tenant_uuid>
X-Correlation-Id: <request_uuid>  # Optional; generated if not provided
```

Service-to-service calls use Managed Identity tokens (no secrets stored or transmitted).

---

## Core API Endpoints

### Agent Copilot API

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/copilot/session/start` | Initialise a copilot session for an interaction |
| `POST` | `/api/v1/copilot/session/{id}/message` | Submit a new transcript segment |
| `GET` | `/api/v1/copilot/session/{id}/suggestions` | Poll for pending suggestions |
| `POST` | `/api/v1/copilot/session/{id}/feedback` | Submit agent feedback on a suggestion |
| `POST` | `/api/v1/copilot/session/{id}/end` | End session and trigger post-interaction workflows |

### Knowledge API

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/knowledge/query` | Semantic knowledge retrieval with RAG |
| `GET` | `/api/v1/knowledge/sources` | List configured knowledge sources |
| `POST` | `/api/v1/knowledge/index/refresh` | Trigger incremental index refresh |
| `GET` | `/api/v1/knowledge/analytics/gaps` | Retrieve knowledge gap report |

### Summarisation API

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/summary/interaction` | Generate summary for completed interaction |
| `POST` | `/api/v1/summary/casenote` | Generate CRM case note |
| `POST` | `/api/v1/summary/shift` | Generate supervisor shift brief |
| `POST` | `/api/v1/summary/escalation/{interactionId}` | Generate escalation context package |

### Quality API

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/quality/score` | Score a completed interaction |
| `GET` | `/api/v1/quality/flags/{interactionId}` | Retrieve compliance flags for interaction |
| `GET` | `/api/v1/quality/trends` | Quality trend report (with date/team filters) |

---

## Error Response Format (RFC 7807)

```json
{
  "type": "https://genesys-surround.example.com/errors/knowledge-not-found",
  "title": "Knowledge source not found",
  "status": 404,
  "detail": "The requested knowledge source 'servicenow-prod' is not configured for this tenant.",
  "instance": "/api/v1/knowledge/query",
  "correlationId": "8f14e0ff-c1d1-4bd2-bb1e-e3e73ff63af9"
}
```

---

## Event API (Azure Service Bus)

### Published Events (platform → subscribers)

| Topic | Event Type | Description |
|---|---|---|
| `interactions` | `interaction.started` | New interaction received and session initialised |
| `interactions` | `interaction.ended` | Interaction completed; summary available |
| `suggestions` | `suggestion.generated` | New AI suggestion ready for agent |
| `quality` | `flag.raised` | Compliance flag detected in interaction |
| `knowledge` | `index.refreshed` | Knowledge index update completed |

### Consumed Events (source systems → platform)

| Topic | Event Type | Source |
|---|---|---|
| `genesys` | `conversation.started` | Genesys Cloud |
| `genesys` | `transcript.segment` | Genesys Cloud |
| `crm` | `case.created` | D365, Salesforce, ServiceNow, Zendesk |
| `crm` | `case.updated` | D365, Salesforce, ServiceNow, Zendesk |

---

## Rate Limits

| API Group | Rate Limit | Burst |
|---|---|---|
| Copilot Session | 100 req/min per tenant | 200 req/min for 30 sec |
| Knowledge Query | 200 req/min per tenant | 400 req/min for 30 sec |
| Summarisation | 50 req/min per tenant | 100 req/min for 30 sec |
| Quality Score | 200 req/min per tenant | 400 req/min for 30 sec |

Limits are configurable per tenant in the platform administration portal.
