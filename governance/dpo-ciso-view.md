# DPO / CISO View

## Executive Summary

This document addresses the most important questions that a **Data Protection Officer (DPO)** and **Chief Information Security Officer (CISO)** will ask about the Contact Center Foundry platform. It is designed to support internal review, security due diligence, and Data Protection Impact Assessments (DPIA).

---

## What Is Contact Center Foundry?

Contact Center Foundry is an AI platform that provides real-time assistance to contact center agents. It:

- **Reads** interaction data from contact center platforms (e.g., Genesys) and CRM systems
- **Processes** that data using large language models (Azure OpenAI) to generate suggestions for agents
- **Writes** AI-generated content (summaries, case notes) back to CRM systems
- **Does NOT** interact directly with customers — the human agent remains the sole communication interface

---

## Data Processing Summary

| Data Category | Source | Purpose | Retained? |
|---|---|---|---|
| Customer name, contact details | CRM (D365, Salesforce, etc.) | Contextualise agent assistance | Session only; not persisted |
| Interaction transcript (masked) | Genesys (voice/chat) | Generate AI suggestions, summaries | Optional; configurable retention |
| CRM case data | CRM systems | Context for next-best-action | Session only |
| Knowledge base content | SharePoint, ServiceNow, etc. | Ground AI responses | Indexed; no PII |
| Agent identity | Entra ID | Access control, audit | Yes (access log) |
| AI inference log | Platform internal | Audit, explainability, compliance | 2 years (configurable) |

**Key point**: Raw PII (names, contact details, health information, card numbers) is **masked before any AI processing**. Azure OpenAI models never receive unmasked PII.

---

## Data Residency & Sovereignty

| Requirement | Platform Response |
|---|---|
| Data must remain in the EU | Platform deployed in EU Azure regions; data residency guaranteed |
| Data must remain in the UK | UK Azure regions available; UK South / UK West |
| Data must not be used for model training | Azure OpenAI does not use customer data for model training (contractually guaranteed) |
| Data must be stored in our tenant | All data stored in customer's own Azure subscription |

---

## DPIA Considerations

### Is a DPIA Required?

A DPIA is likely required when:
- Processing involves systematic monitoring of employees (agent interaction monitoring)
- Processing is at scale (all interactions, all agents)
- Processing uses new technology (LLMs, AI-based quality scoring)
- Processing involves special category data (health information in healthcare contact centers)

Contact Center Foundry provides a **DPIA template** that organisations can adapt, covering:
- Processing activities and purposes
- Necessity and proportionality assessment
- Risk identification and mitigation
- Consultation requirements

### Key DPIA Points

**Agent monitoring**: The platform processes agent interaction data to provide assistance and quality scoring. This constitutes systematic monitoring of employees. Legal basis options: legitimate interest (performance of contract, legitimate business interest), or explicit policy/agreement.

**Customer data**: Customer data is processed to contextualise AI assistance. Legal basis: legitimate interest (contract performance, service delivery). Customers should be informed of AI-assisted service delivery in privacy notices.

**Automated decisions**: The platform does not make automated decisions about customers. AI suggestions are reviewed and acted on by human agents. This removes the Art. 22 GDPR automated decision-making risk.

---

## Access Control Summary

| Who Accesses What | Access Scope |
|---|---|
| Agent | Own interaction data; authorised knowledge sources; customer context (CRM-permissioned) |
| Supervisor | Team interaction data; quality scores; no raw PII beyond their CRM access |
| Quality Analyst | Scored interactions (masked by default); compliance flags |
| Platform Admin | Platform configuration only; no access to interaction content |
| CISO/DPO | Security and compliance dashboards; audit logs (read-only) |
| Microsoft | Azure platform support (break-glass, subject to customer approval); no AI model training access |

---

## Audit & Accountability

Every AI inference produces an immutable audit record containing:

- Timestamp (UTC)
- Tenant and agent identifiers
- Interaction reference
- Prompt template version used
- Knowledge sources retrieved (document titles and IDs; not content)
- Model version
- Hashed AI response
- Agent action taken (suggestion accepted / dismissed)

Audit logs are stored in **Azure Blob Storage with immutability policies** (WORM — Write Once Read Many). Logs cannot be modified or deleted (except by explicit retention policy expiry). Access to audit logs is role-controlled and itself audited.

---

## Incident Response

In the event of a suspected security or privacy incident:

| Event | Response |
|---|---|
| Suspected data breach | Platform stops processing; incident logged; notification workflow triggered within 24 hours |
| Unauthorised access detected | Account suspended; CISO and DPO notified; forensic log export |
| AI model malfunction | Affected component suspended; Platform Owner and CISO notified; agents notified to work without AI assist |
| Compliance flag volume spike | Automated alert to Compliance Officer; review workflow triggered |

---

## Questions & Answers

**Q: Does Microsoft see our customer data?**
A: No. The platform is deployed in the customer's Azure tenant. Microsoft cannot access customer data except under break-glass support scenarios that require customer authorisation and are logged.

**Q: Does Azure OpenAI use our data for training?**
A: No. Azure OpenAI has contractual guarantees that customer data is not used for model training or improvement.

**Q: How long is conversation data retained?**
A: Raw transcripts are not retained by default. AI inference logs (masked) are retained for 2 years by default, configurable per regulatory requirement. Compliance flags are retained for 7 years by default.

**Q: Can we audit what the AI said to our agents?**
A: Yes. The audit log contains the exact AI suggestion (hashed), the prompt version, and the knowledge sources used, for every interaction.

**Q: What happens if we want to switch off the AI for a specific agent group or topic?**
A: Platform administrators can disable AI assistance by agent group, topic, or channel with immediate effect, via the administration portal.

**Q: How do we get a copy of all data held for a specific customer (GDPR Subject Access Request)?**
A: The platform includes a data subject request workflow that identifies and exports all platform-held records for a given customer identifier.
