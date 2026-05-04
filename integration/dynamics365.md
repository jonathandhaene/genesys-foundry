# Microsoft Dynamics 365 Integration

## Integration Overview

Microsoft Dynamics 365 is a natural complement to Genesys Surround given the shared Microsoft technology foundation. The integration leverages the **Microsoft Dataverse API**, **Power Platform connectors**, and **Microsoft Graph**.

---

## Authentication

- **OAuth 2.0** via Microsoft Entra ID (same identity provider as the Genesys Surround platform)
- Dedicated Entra ID app registration with Dataverse API scoped permissions
- Service principal with minimum required roles
- Credentials managed via Azure Key Vault + Managed Identity chain

**Required API permissions:**

| Permission | Scope | Reason |
|---|---|---|
| Dynamics CRM user_impersonation | Delegated | Agent context reads (impersonating agent identity) |
| Dataverse API access | Application | Background sync, case writeback |

---

## Read Operations (CRM Context)

At the start of each interaction, the platform fetches customer context from Dynamics 365:

```
GET /api/data/v9.2/contacts?
  $filter=emailaddress1 eq '{email}' or telephone1 eq '{phone}'
  &$select=fullname,accountid,customerid,createdon
  &$expand=parentcustomerid_account($select=name,accountnumber,statecode)
```

**Data retrieved:**

| Data | D365 Entity | Purpose |
|---|---|---|
| Contact record | `contact` | Name, tier, preferences |
| Account record | `account` | Organisation, contract, health score |
| Open cases | `incident` | Current issues and status |
| Recent cases (30 days) | `incident` | Interaction history |
| SLA entitlement | `entitlement` | SLA status and breach risk |
| Knowledge articles | `knowledgearticle` | KB access for knowledge layer |

---

## Write Operations (Case Notes & Summaries)

After interaction completion, AI-generated content is written back to Dynamics 365:

### Case Note (Annotation)

```
POST /api/data/v9.2/annotations
{
  "notetext": "<AI-generated case note content>",
  "subject": "AI Interaction Summary — {datetime}",
  "objectid_incident@odata.bind": "/incidents({caseId})",
  "isdocument": false
}
```

### Interaction Activity Log

```
POST /api/data/v9.2/phonecalls
{
  "subject": "AI-Assisted Interaction — {interactionId}",
  "description": "<Structured summary>",
  "directioncode": true,
  "regardingobjectid_incident@odata.bind": "/incidents({caseId})",
  "statecode": 1,
  "statuscode": 2
}
```

---

## Knowledge Base Integration

Dynamics 365 Knowledge Articles are indexed into the Genesys Surround Knowledge Layer:

```
GET /api/data/v9.2/knowledgearticles?
  $filter=statecode eq 3        # Published articles only
  &$select=title,content,keywords,modifiedon,languagelocaleid
```

- Refreshed incrementally every 4 hours (configurable)
- Access control: only articles visible to the agent's security role are returned at query time
- Linked to specific products/topics via D365 category hierarchy

---

## Power Platform Integration (Optional)

For organisations using Power Automate, Genesys Surround publishes events to a **custom connector** that can trigger Power Automate flows:

- On interaction summary available → trigger D365 case update flow
- On compliance flag → trigger D365 compliance workflow
- On knowledge gap detected → trigger KB management notification flow

---

## Configuration Reference

```yaml
connector:
  id: dynamics365-prod
  type: dynamics365
  instance_url: https://yourorg.crm4.dynamics.com
  auth:
    type: entra_service_principal
    tenant_id_secret: kv://entra-tenant-id
    client_id_secret: kv://d365-client-id
    client_secret_secret: kv://d365-client-secret
  features:
    context_load: true
    knowledge_index: true
    case_note_writeback: true
    activity_log: true
  knowledge:
    refresh_interval_hours: 4
    language_filter: ["en", "fr", "de"]   # Configurable
```
