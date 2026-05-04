# Platform Overview

The Genesys Surround platform is composed of discrete, independently deployable services. This modular architecture allows organisations to adopt the platform incrementally — starting with the capabilities most aligned to immediate business priorities.

## Core Services

| Service | Purpose | Primary Consumer |
|---|---|---|
| **Agent Copilot Service** | Real-time assistance during live interactions | Agents |
| **Knowledge Service** | Semantic search and RAG across enterprise KB | Agents, Supervisors |
| **Summarisation Service** | Post-interaction and mid-interaction summaries | Agents, Supervisors, QA |
| **Quality Intelligence Service** | AI-powered interaction scoring and flagging | Supervisors, QA, Compliance |
| **Supervisor Intelligence Service** | Team insights, coaching recommendations, shift briefs | Supervisors |
| **Knowledge Curation Service** | KB gap detection, content quality, usage analytics | Knowledge Managers |
| **Integration Gateway** | Normalised API and event connectivity to source systems | All |

## Deployment Model

All services are containerised and deployed to **Azure Kubernetes Service (AKS)**:
- Each service is independently versioned and deployable
- Services communicate via Azure Service Bus (async) and REST (sync)
- Secrets managed via Azure Key Vault
- Infrastructure defined as code (Bicep)

## Contents

| File | Description |
|---|---|
| [`components.md`](./components.md) | Detailed component specifications |
| [`api-reference.md`](./api-reference.md) | API design patterns and endpoint reference |
| [`extensibility.md`](./extensibility.md) | How to extend and customise the platform |
