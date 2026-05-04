# Responsible AI

## Commitment

Genesys Surround is built in alignment with **Microsoft's Responsible AI Standard** and the six responsible AI principles. This document describes how those principles are operationalised within the platform.

---

## Principle 1: Fairness

**Commitment**: AI systems should treat all people fairly and not create or reinforce unfair bias.

### Implementation

- **Monitoring**: Agent suggestion quality monitored across demographic segments (where data is available and legally permissible) to detect disparate performance
- **Knowledge quality**: KB content reviewed for inclusive, non-discriminatory language before indexing
- **Rubric design**: Quality scoring rubrics reviewed to ensure no systematic disadvantage to agents serving particular customer groups
- **Model selection**: Azure OpenAI models selected with Microsoft's responsible AI evaluation process completed

### Ongoing Actions
- Quarterly bias review of AI suggestion patterns by agent group and topic
- Stakeholder feedback channel for agents to report potentially biased suggestions
- Third-party fairness audit annually

---

## Principle 2: Reliability & Safety

**Commitment**: AI systems should perform reliably and safely across intended uses and unintended situations.

### Implementation

- **Confidence thresholds**: Suggestions below the configured confidence threshold are withheld or flagged as "low confidence"
- **Grounding requirement**: Responses grounded in retrieved knowledge are flagged as such; responses that rely solely on model weights are marked differently
- **Output validation**: Every AI response validated against format, length, and content safety rules before delivery
- **Fallback design**: When AI cannot provide a confident answer, the agent is shown "No confident answer found — please search manually" rather than a potentially incorrect response
- **Graceful degradation**: Platform designed to continue operating (in reduced mode) if any AI component is unavailable

### Testing Standards
- Every prompt template has defined test cases for accuracy and refusal behaviour
- Red-teaming conducted quarterly on agent copilot and knowledge service
- Regression testing on every model upgrade

---

## Principle 3: Privacy & Security

**Commitment**: AI systems should be secure and respect privacy.

### Implementation

- PII masking before AI processing (described in [`security.md`](./security.md))
- No customer data used for model training (Azure OpenAI agreement)
- Data residency controls enforce geography boundaries
- Access control at every layer of the platform

> See [`security.md`](./security.md) and [`compliance.md`](./compliance.md) for detailed controls.

---

## Principle 4: Inclusiveness

**Commitment**: AI systems should empower everyone and engage people.

### Implementation

- **Multilingual support**: Platform supports knowledge retrieval and response generation in the agent's and customer's language
- **Accessibility**: Agent Companion UI meets WCAG 2.1 AA standards
- **Varying expertise levels**: AI suggestions calibrated to be helpful for both new and experienced agents
- **Neuroinclusive design**: Interface designed to minimise cognitive overload; information presented progressively

---

## Principle 5: Transparency

**Commitment**: AI systems should be understandable.

### Implementation

- **Agent awareness**: Agents always know they are receiving AI assistance (clearly labelled in UI)
- **Source attribution**: Every AI suggestion shows the knowledge sources it is based on
- **Confidence display**: Confidence score shown to agent for every suggestion
- **Explainability**: Supervisors and compliance officers can inspect exactly what the AI said, what context it used, and which model version produced it
- **No hidden automation**: No automated responses to customers without agent review and approval

### Customer Transparency
Organisations should inform customers that AI is used to support agents. Genesys Surround does not directly interact with customers — the agent remains the communication interface.

---

## Principle 6: Accountability

**Commitment**: People should be accountable for AI systems.

### Implementation

- **AI Platform Owner**: Designated role responsible for model governance, prompt approval, and platform policy
- **Human decision authority**: AI provides recommendations and suggestions; the human agent retains full decision-making authority for all customer interactions
- **Audit trail**: Every AI inference is logged and traceable to the individual, the prompt version, and the context used
- **Escalation paths**: Defined processes for when AI behaviour is questioned or appears incorrect
- **Incident response**: Documented playbook for AI-related incidents (incorrect suggestion, compliance breach, model failure)
- **Model change governance**: Model upgrades require AI Platform Owner approval; staged rollout with monitoring

---

## AI Governance Roles & Accountabilities

| Role | Accountability |
|---|---|
| AI Platform Owner | Overall platform AI governance; model registry; prompt approval |
| CISO | Security of AI systems and data |
| DPO | Privacy compliance of AI data processing |
| Compliance Officer | Regulatory compliance of AI outputs |
| Supervisor | Oversight of AI suggestions used by their team |
| Agent | Responsible use of AI suggestions in customer interactions |

---

## Responsible AI Incident Process

If an AI behaviour is identified as potentially harmful, biased, or incorrect:

1. **Report**: Agent or supervisor reports via platform feedback mechanism
2. **Triage**: AI Platform Owner triages within 24 hours
3. **Assess**: Impact assessment conducted (how many interactions affected?)
4. **Contain**: Affected prompt template or model version suspended if risk confirmed
5. **Remediate**: Root cause analysis, fix, and re-testing
6. **Communicate**: Stakeholders notified; audit record created
7. **Review**: Post-incident review to improve detection and prevention
