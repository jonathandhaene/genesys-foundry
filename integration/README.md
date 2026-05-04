# Integration

## Integration Philosophy

Genesys Surround integrates with existing systems by **wrapping around them** — never modifying them. Every integration follows these principles:

1. **API-first**: All integration via official, documented APIs
2. **Event-driven**: Prefer asynchronous events over synchronous polling where possible
3. **Read-heavy, write-surgical**: Read broadly for context; write back only AI-generated content (summaries, case notes)
4. **No intrusion**: No changes to the source system's schema, workflows, or UI
5. **Adapter isolation**: Each connector is isolated; a change to one does not affect others

## Supported Integrations

| System | Type | File |
|---|---|---|
| Genesys Cloud / Engage | Contact Center | [`genesys.md`](./genesys.md) |
| Microsoft Dynamics 365 | CRM | [`dynamics365.md`](./dynamics365.md) |
| Salesforce | CRM | [`salesforce.md`](./salesforce.md) |
| ServiceNow | ITSM | [`servicenow.md`](./servicenow.md) |
| Zendesk | CRM / Support | [`zendesk.md`](./zendesk.md) |

## Integration Gateway

All integrations flow through the **Integration Gateway** — a centralised adapter management layer built on Azure API Management:

```
External System
      │
      │ (API / Webhooks / Events)
      ▼
┌─────────────────────────────┐
│    Integration Gateway      │
│                             │
│  ┌─────────────────────┐   │
│  │  Connector Library   │   │
│  │  (per system)        │   │
│  └──────────┬──────────┘   │
│  ┌──────────▼──────────┐   │
│  │  UIM Normaliser      │   │
│  └──────────┬──────────┘   │
│  ┌──────────▼──────────┐   │
│  │  Event Publisher     │   │
│  └─────────────────────┘   │
└─────────────────────────────┘
      │
      ▼
Platform Event Bus (Azure Service Bus)
```

## Adding a New Integration

To add a new source system:
1. Implement the connector interface (see [`/platform/extensibility.md`](../platform/extensibility.md))
2. Map system events to the Unified Interaction Model
3. Register the connector in the Integration Gateway
4. Configure authentication credentials in Azure Key Vault
5. Test with the platform's connector validation tool
