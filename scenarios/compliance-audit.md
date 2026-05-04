# Scenario: Compliance & Audit Support

## Overview

In regulated industries — financial services, healthcare, public sector, insurance — every agent interaction carries compliance obligations. Traditional QA processes review only a small sample of interactions, leaving significant compliance risk undetected.

Genesys Surround provides **AI-powered compliance monitoring and audit support** that achieves 100% interaction coverage, real-time compliance flagging, and a complete, tamper-evident audit trail.

---

## Compliance Capabilities

### 1. Real-Time Compliance Monitoring

During live interactions, the platform monitors for:

| Flag Type | Example | Action |
|---|---|---|
| PCI-DSS | Credit card number spoken aloud | Alert agent + supervisor; trigger pause/mask recording |
| GDPR | Data deletion request not acknowledged | Alert agent with required response |
| Financial Advice | Non-compliant investment language | Real-time script guidance to agent |
| Mandatory Disclosures | Required legal statement not delivered | Prompt agent before interaction closes |
| Sensitive Topics | Customer expresses vulnerability (financial distress, health) | Trigger support protocol |

### 2. Post-Interaction Compliance Scoring

Every completed interaction is automatically scored:

```json
{
  "interactionId": "int-56789",
  "complianceScore": 82,
  "passedChecks": [
    "greeting_script",
    "security_verification",
    "product_suitability_disclosure"
  ],
  "failedChecks": [
    {
      "checkId": "annual_return_disclaimer",
      "description": "Agent did not deliver required past performance disclaimer",
      "timestamp": "2025-10-15T14:32:11Z",
      "transcript_ref": "turn_14",
      "severity": "high",
      "regulation": "FCA COBS 4.5.2"
    }
  ],
  "flags": [],
  "auditReady": true
}
```

### 3. Audit Trail

Every AI inference and compliance check is recorded in an immutable audit log:

```
Audit Record: interaction:int-56789 / compliance-check:annual_return_disclaimer
──────────────────────────────────────────────────────────────────────────────
Timestamp:        2025-10-15T14:32:15.421Z
Tenant:           acme-corp
Interaction:      int-56789
Check Type:       mandatory_disclosure_detect
Prompt Version:   compliance-fca-disclosure-v2.1
Context Used:     turns 12–16 of transcript
Model:            gpt-4o (2024-08-06)
Result:           FAIL — disclosure phrase not detected
Confidence:       0.94
Reviewer:         automated
Stored:           audit-log/2025/10/15/int-56789-compliance.json (immutable)
```

---

## Regulatory Coverage by Module

### GDPR (General Data Protection Regulation)

| Obligation | Platform Support |
|---|---|
| Data subject access requests | Detection and workflow trigger |
| Erasure requests ("right to be forgotten") | Detection and case routing |
| Consent verification | Agent prompt on data collection |
| Purpose limitation | Data use scoped to interaction purpose |
| Data retention | Configurable retention periods per data type |

### PCI-DSS

| Obligation | Platform Support |
|---|---|
| Card number in voice/text | Real-time detection and masking before AI processing |
| Recording scope | Trigger pause/resume for payment segments |
| Agent awareness | Real-time alert to stop capturing card data verbally |

### Financial Services (FCA / MiFID II)

| Obligation | Platform Support |
|---|---|
| Suitability assessment language | Post-interaction LLM analysis |
| Past performance disclaimers | Script compliance monitoring |
| Communication records | Full interaction archiving with AI annotations |
| Complaint identification | Sentiment + language signal detection |

### HIPAA (Healthcare)

| Obligation | Platform Support |
|---|---|
| PHI detection in transcripts | Pre-AI PII masking |
| Minimum necessary access | RBAC scoping to treatment context |
| Audit controls | Full access and inference logging |

---

## Audit Preparation

When an internal or external audit is initiated, the platform can produce:

1. **Interaction Audit Package**: complete record of a specific interaction including:
   - Full transcript (with PII masked or available based on authorisation)
   - AI suggestions shown to agent (with sources)
   - Compliance checks passed/failed
   - Agent actions taken
   - Case outcome

2. **Compliance Trend Report**: aggregated compliance pass/fail rates by:
   - Agent / team
   - Topic / product
   - Channel
   - Time period
   - Regulation / rule

3. **Model Governance Log**: record of which AI model version was active at any given time, with prompt versions used.

---

## DPO / CISO Assurance Points

| Question | Answer |
|---|---|
| What data does the AI see? | Masked interaction data only; raw PII never reaches the AI layer |
| Where is data stored? | Customer's Azure tenant; no data leaves the organisation's cloud boundary |
| Is conversation content retained? | Configurable; no default persistence of raw transcripts beyond operational need |
| Who can access audit logs? | Defined RBAC roles only; access itself is audited |
| Can we prove what the AI said? | Yes — every response logged with prompt version, context sources, and model version |
| What if the AI makes a mistake? | Human-in-the-loop processes defined for all high-risk use cases |

> See [`/governance/dpo-ciso-view.md`](../governance/dpo-ciso-view.md) for the complete CISO/DPO documentation.
