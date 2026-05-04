# Compliance

## Regulatory Philosophy

Contact Center Foundry does not treat compliance as a checkbox. It is designed so that compliance obligations are **embedded into the platform's data handling, AI processing, and audit capabilities** — making it easier to be compliant than not.

---

## GDPR (General Data Protection Regulation)

### Key Obligations & Platform Response

| GDPR Article | Obligation | Platform Support |
|---|---|---|
| Art. 5 | Data minimisation, purpose limitation | AI processes only what is necessary; purpose scoped per use case |
| Art. 6 | Lawful basis for processing | Processing justified by legitimate interest (agent support); configurable consent integration |
| Art. 17 | Right to erasure | Interaction data deletion workflows; propagates to audit logs where legally required |
| Art. 20 | Data portability | Interaction history exportable in structured format |
| Art. 22 | Automated decision-making | AI provides recommendations; human agent is always the decision-maker |
| Art. 25 | Privacy by design | PII masking, data minimisation, tenant isolation built into architecture |
| Art. 30 | Records of processing | Processing records maintained; exportable for DPA requests |
| Art. 32 | Security of processing | Encryption, access control, audit logging |
| Art. 35 | DPIA | DPIA template available for high-risk processing scenarios |

### Data Residency
- Platform deployed in customer's chosen Azure region
- Data remains within the selected geography (EU, UK, US, etc.)
- Azure OpenAI used with **data residency guarantees** (no data used for model training)

### Data Subject Requests
When a data subject requests access or erasure:
1. Platform identifies all interaction records containing the subject's identifier
2. Generates a data extract (access request) or deletion workflow (erasure request)
3. Deletion propagates across all platform stores
4. Audit record of the request and action retained (legal obligation)

---

## HIPAA (Health Insurance Portability and Accountability Act)

### Applicability
HIPAA controls apply when the platform is used in healthcare contact center contexts involving Protected Health Information (PHI).

| HIPAA Requirement | Platform Implementation |
|---|---|
| PHI detection | Azure AI Language PHI entity recognition |
| Minimum necessary access | RBAC-scoped to treatment context; agents see only relevant PHI |
| Access logging | All PHI-adjacent data access logged with user, timestamp, and purpose |
| Transmission security | TLS 1.3 in transit; encrypted at rest |
| Business Associate Agreement | Azure BAA covers Azure OpenAI and underlying services |
| Audit controls | Immutable audit trail for all PHI interactions |

---

## PCI-DSS (Payment Card Industry Data Security Standard)

### Scope Minimisation Strategy
The primary PCI-DSS compliance strategy is **scope minimisation** — ensuring that payment card data never reaches platform storage or AI models.

| PCI-DSS Requirement | Platform Response |
|---|---|
| Req. 3: Protect stored data | Card numbers detected and masked before any processing |
| Req. 4: Encrypt transmission | TLS 1.3 for all data in transit |
| Req. 7: Restrict access by need | RBAC; agents only see interaction data they are authorised for |
| Req. 10: Track access | All access to interaction data logged |
| Req. 12: Maintain security policy | Platform security policy documented and reviewable |

**Voice PCI-DSS**: When a customer is about to provide card details, the platform can trigger a **pause-recording** signal to Genesys, ensuring card data is not captured in the transcript.

---

## Financial Services (FCA / MiFID II / FINRA)

| Regulation | Obligation | Platform Support |
|---|---|---|
| FCA COBS 4.5 | Suitability and past performance disclosure | Automated compliance monitoring of disclosure language |
| MiFID II Art. 16 | Communication recording | Full interaction archiving with AI annotations |
| FCA SYSC | Operational resilience | High-availability architecture; disaster recovery documented |
| FINRA Rule 3110 | Supervision of communications | AI-powered 100% interaction review |

---

## EU AI Act

Contact Center Foundry's use in contact centers may qualify as a **high-risk AI system** under Annex III of the EU AI Act (specifically: AI systems used in employment and critical private services). The platform is designed to meet anticipated high-risk requirements:

| EU AI Act Requirement | Platform Support |
|---|---|
| Technical documentation | Architecture documentation maintained in this repository |
| Risk management system | Documented risk assessments; mitigations in place |
| Data governance | Training data provenance; RAG grounding |
| Transparency | Agents always know they are receiving AI assistance |
| Human oversight | AI provides suggestions only; agent retains full decision authority |
| Accuracy, robustness, security | Confidence thresholds; output validation; security controls |
| Logging | Comprehensive AI inference logging |

---

## Data Retention Policy

| Data Type | Default Retention | Configurable |
|---|---|---|
| Raw interaction transcript | Not retained by default | Optional; up to 90 days |
| AI inference log (masked) | 2 years | Yes |
| Compliance flags | 7 years | Configurable per regulation |
| Audit log | 10 years | Yes |
| Knowledge index | Aligned to source document lifecycle | Yes |
| CRM data cache | Session only (not persisted) | No |
