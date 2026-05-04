# Security Model

## Security Design Principles

The Genesys Surround security model follows Microsoft's **Zero Trust** framework: never trust, always verify. Every request — whether from an agent, an integration, or a service — must be authenticated, authorised, and audited.

---

## Identity & Access Management

### Authentication
- All users authenticated via **Microsoft Entra ID** (formerly Azure AD)
- Supports MFA, Conditional Access policies, and phishing-resistant credentials (FIDO2)
- Service-to-service authentication uses **Managed Identities** (no secrets stored or transmitted)
- Integration connectors use OAuth 2.0 client credentials flow with scoped permissions

### Authorisation
- **Role-Based Access Control (RBAC)** implemented at every service boundary
- Platform-defined roles (see table below)
- Custom roles configurable per tenant
- Principle of least privilege enforced by default

| Role | Access Scope |
|---|---|
| `agent` | Own session data, authorised knowledge sources, customer context (per CRM permissions) |
| `supervisor` | Team interaction data, quality scores, coaching insights |
| `knowledge_manager` | KB indexing, gap reports, content curation |
| `quality_analyst` | Scored interactions, compliance flags, QA reports |
| `platform_admin` | Platform configuration, connector management, user administration |
| `audit_reader` | Read-only access to audit logs and compliance reports |
| `ciso_dpo` | Security and compliance dashboards, data lineage, model governance |

### Tenant Isolation
- Every tenant's data is logically isolated at all layers
- Cross-tenant data access is architecturally impossible (enforced by service-level tenant ID validation)
- Azure resources scoped to tenant via dedicated namespaces and resource groups

---

## Data Encryption

| State | Standard |
|---|---|
| **In Transit** | TLS 1.3 for all service-to-service and client-to-service communication |
| **At Rest** | AES-256 encryption for all storage (Azure Blob, Cosmos DB, AI Search) |
| **Keys** | Customer-managed keys (CMK) in Azure Key Vault (configurable) |
| **Secrets** | All secrets stored in Azure Key Vault; no secrets in code or configuration |

---

## Network Security

- All platform services deployed to **private Azure Virtual Networks**
- No public internet exposure of backend services
- API Management gateway is the only public-facing entry point
- **Private Endpoints** used for all Azure PaaS services (Storage, Cosmos DB, AI Search, OpenAI)
- Azure DDoS Protection Standard enabled
- Web Application Firewall (WAF) on API Management
- Network Security Groups (NSG) enforce least-privilege network flows

---

## PII Detection & Protection

Before any interaction data reaches the AI Layer, it passes through the PII protection pipeline:

1. **Detection**: Azure AI Language detects PII entities (names, phone numbers, emails, account numbers, health information, card numbers)
2. **Decision**: Based on configurable policy — mask, tokenise, or block
3. **Masking**: PII replaced with category tokens (e.g., `[PHONE_NUMBER]`, `[CREDIT_CARD]`)
4. **Logging**: Masking actions recorded in audit log with entity category (not value)

Raw PII never reaches the AI Layer.

---

## Threat Model & Mitigations

| Threat | Mitigation |
|---|---|
| Prompt injection attacks | Input validation, system prompt hardening, output filtering |
| Data exfiltration via LLM | Response validation, grounding checks, output content safety |
| Unauthorised knowledge access | Source-level access control enforced at query time |
| Credential compromise | MFA, Managed Identity, no long-lived secrets |
| Insider threat | Audit logging of all data access; Just-in-Time (JIT) privileged access |
| Supply chain (LLM model) | Model pinned to approved versions; Azure OpenAI (Microsoft-managed) |
| API abuse | Rate limiting, API Management throttling, anomaly detection |

---

## Security Operations

### Logging & Monitoring
- All platform activity logged to **Azure Monitor / Log Analytics**
- Security events forwarded to **Microsoft Sentinel** for SIEM analysis
- Anomaly detection alerts for unusual access patterns
- Platform health and availability monitoring via Application Insights

### Incident Response
- Defined incident response playbook for AI-related security events
- 24/7 alerting via Azure Monitor action groups
- Integration with enterprise SOC tools via Sentinel

### Vulnerability Management
- Container images scanned at build and runtime via **Microsoft Defender for Containers**
- Dependency vulnerability scanning via **Dependabot** and **Microsoft Defender for DevOps**
- Penetration testing conducted quarterly by independent third party

---

## Security Certifications (Target)

| Standard | Status |
|---|---|
| ISO 27001 | Architecture aligned; certification via Azure foundation |
| SOC 2 Type II | Inherits from Azure; customer-specific audit available |
| Cyber Essentials Plus | Architecture compliant |
| NCSC Cloud Security Principles | Alignment mapped |
