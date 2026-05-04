# Governance

Contact Center Foundry is built for regulated and security-conscious environments. Governance is not an add-on — it is a foundational platform capability.

## Contents

| Document | Audience | Description |
|---|---|---|
| [`security.md`](./security.md) | CISO, Security Architecture | Security model, controls, and threat mitigation |
| [`compliance.md`](./compliance.md) | DPO, Compliance, Legal | Regulatory coverage and data handling |
| [`responsible-ai.md`](./responsible-ai.md) | AI Governance, Ethics | Responsible AI principles and implementation |
| [`dpo-ciso-view.md`](./dpo-ciso-view.md) | DPO, CISO | Executive governance summary |

## Governance Framework

The platform governance framework is structured around five pillars:

```
┌──────────────────────────────────────────────────────────┐
│                  GOVERNANCE FRAMEWORK                     │
├──────────────┬───────────────┬──────────────┬────────────┤
│   Security   │  Compliance   │  Responsible │  Privacy   │
│              │               │     AI       │            │
├──────────────┼───────────────┼──────────────┼────────────┤
│ Identity &   │ GDPR · HIPAA  │ Fairness     │ PII masking│
│ Access Ctrl  │ PCI-DSS · FSI │ Reliability  │ Data min.  │
│ Encryption   │ ISO 27001     │ Transparency │ Consent    │
│ Network Sec  │ EU AI Act     │ Accountability│ Retention  │
│ Threat Mgmt  │ NIS2          │ Oversight    │ Subject Rts│
└──────────────┴───────────────┴──────────────┴────────────┘
                              │
                    ┌─────────▼─────────┐
                    │  AUDIT & TRACING  │
                    │  (all pillars)    │
                    └───────────────────┘
```

## Governance Roles

| Role | Key Responsibilities |
|---|---|
| AI Platform Owner | Model registry, prompt governance, platform policy |
| Data Protection Officer (DPO) | DPIA, data subject rights, processing agreements |
| CISO | Security architecture, access policy, incident response |
| Compliance Officer | Regulatory mapping, audit readiness |
| Knowledge Manager | Content quality, source authorisation, KB governance |
