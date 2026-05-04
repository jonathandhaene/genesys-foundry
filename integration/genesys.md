# Genesys Integration

## Integration Overview

Genesys is the **primary contact center platform** in the Genesys Surround reference architecture. The integration connects to Genesys Cloud (preferred) and Genesys Engage (on-premises) via their official APIs and notification services.

**Critical principle**: The Genesys telephony core is **not modified**. No custom code is injected into the Genesys platform. All integration is via Genesys' published APIs and event services.

---

## Genesys Cloud Integration

### Authentication
- OAuth 2.0 Client Credentials flow using a dedicated Genesys Cloud OAuth client
- Scoped permissions (minimum required only)
- Credentials stored in Azure Key Vault; rotated automatically

### Event Integration (Primary Path)

Genesys Cloud provides **Notifications API** (WebSocket) and **Event Bridge** for real-time event delivery.

**Events consumed:**

| Event | Trigger | Platform Action |
|---|---|---|
| `v2.conversations.{id}.participants.{pid}` | New conversation segment | Start AI session, load CRM context |
| `v2.conversations.{id}.messages` | New chat/email message | Trigger AI suggestion |
| `v2.conversations.{id}.recordings.transcripts` | New transcript segment (voice) | Trigger AI suggestion |
| `v2.conversations.{id}.participants.{pid}.wrapup` | After-call work started | Trigger summarisation workflow |
| `v2.conversations.{id}` | Conversation ended | Finalise session, generate summary |
| `v2.routing.queues.{qid}.conversations` | Queue activity | Supervisor intelligence feed |

### REST API Integration

**Read operations:**

| Data | API Endpoint | When Used |
|---|---|---|
| Conversation details | `GET /api/v2/conversations/{conversationId}` | Session initialisation |
| Participant data | `GET /api/v2/conversations/{id}/participants` | Agent identity mapping |
| Queue metrics | `GET /api/v2/analytics/queues/observations/query` | Supervisor dashboard |
| Recording transcription | `GET /api/v2/conversations/{id}/recordings` | Post-call processing |

**Write operations:**

| Action | API Endpoint | Content Written |
|---|---|---|
| Interaction note | `POST /api/v2/conversations/{id}/participants/{pid}/wrapupcodes` | AI-generated summary as wrap-up note |
| External contact tag | `PATCH /api/v2/externalcontacts/contacts/{id}` | Interaction outcome tagging |

### Agent Companion Integration

The Agent Companion UI is delivered as a **Genesys AppFoundry application** — a web panel embedded in the Genesys Agent Desktop using the **Genesys Cloud Client App SDK**.

This means:
- No separate login required (SSO via Genesys Cloud session)
- Panel appears automatically when an interaction is assigned
- Agent sees AI suggestions without leaving the Genesys interface
- No modification to Genesys Agent Desktop configuration beyond AppFoundry app installation

---

## Genesys Engage Integration

Genesys Engage (on-premises) is supported via:

- **Genesys Interaction SDK** for event capture
- **Genesys GWS (Genesys Web Services)** REST API for data access
- **Genesys Platform SDK** for supervisor integration

Note: Engage integration requires an on-premises or hybrid deployment component. See the deployment guide for Engage-specific network requirements.

---

## Configuration Reference

```yaml
# Integration Gateway - Genesys Cloud Connector Configuration
connector:
  id: genesys-cloud-prod
  type: genesys_cloud
  region: eu-west-2           # Genesys Cloud region
  auth:
    type: oauth2_client_credentials
    client_id_secret: kv://genesys-cloud-client-id
    client_secret_secret: kv://genesys-cloud-client-secret
  events:
    transport: websocket       # or event_bridge
    subscriptions:
      - v2.conversations.*
      - v2.routing.queues.*
  features:
    real_time_assist: true
    transcription: true
    wrap_up_notes: true
    supervisor_feed: true
  agent_companion:
    delivery: appfoundry
    panel_width: 400px
```

---

## Security Considerations

- Genesys API credentials scoped to minimum required permissions
- No access to Genesys Admin or configuration APIs
- All webhook payloads validated with Genesys notification signature
- Connector runs in isolated network segment; cannot be reached from internet
