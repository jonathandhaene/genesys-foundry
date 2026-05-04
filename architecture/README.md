# Architecture Reference Guide

This directory contains the detailed reference architecture for the Genesys Surround platform.

## Contents

| File | Description |
|---|---|
| [`layers.md`](./layers.md) | Deep-dive into each of the five platform layers |
| [`diagrams.md`](./diagrams.md) | Text descriptions of all reference architecture diagrams |

## Architecture Principles

The Genesys Surround architecture is governed by the following core principles:

1. **Surround, don't embed** — AI wraps around existing systems via APIs and events
2. **Loose coupling** — each platform component communicates through well-defined interfaces
3. **Event-driven integration** — all cross-system communication is asynchronous and event-based
4. **Knowledge centralisation** — one unified knowledge layer serves all AI capabilities
5. **Governance by default** — every component enforces security, privacy, and auditability
6. **Horizontal scalability** — stateless services, designed for cloud-native deployment

## Quick Reference: Five-Layer Model

```
┌─────────────────────────────────────────────────────────────────────────┐
│  LAYER 5 – GOVERNANCE                                                   │
│  Identity · Access · Compliance · Audit · Responsible AI                │
├─────────────────────────────────────────────────────────────────────────┤
│  LAYER 4 – AI                                                           │
│  LLM (GPT-4o) · Embeddings · Prompt Framework · Output Validation       │
├─────────────────────────────────────────────────────────────────────────┤
│  LAYER 3 – KNOWLEDGE                                                    │
│  RAG Engine · Semantic Index · CRM Data · Policy Store                  │
├─────────────────────────────────────────────────────────────────────────┤
│  LAYER 2 – ORCHESTRATION                                                │
│  Copilot Agents · Workflow Engine · Event Router · Context Manager      │
├─────────────────────────────────────────────────────────────────────────┤
│  LAYER 1 – INTERACTION                                                  │
│  Voice · Chat · Email · Social · Messaging · CRM Events                 │
└─────────────────────────────────────────────────────────────────────────┘
        ↕ API/Event adapters to Genesys, D365, Salesforce, ServiceNow, etc.
```

## Technology Stack Summary

| Layer | Primary Technologies |
|---|---|
| Interaction | Azure Event Grid, Azure Service Bus, REST adapters, WebSocket bridges |
| Orchestration | Microsoft Copilot Studio, Azure Durable Functions, Logic Apps |
| Knowledge | Azure AI Search, Azure OpenAI (embeddings), Cosmos DB, Blob Storage |
| AI | Azure OpenAI (GPT-4o), Azure AI Content Safety, custom prompt library |
| Governance | Microsoft Entra ID, Microsoft Purview, Azure Monitor, Key Vault |

## Deployment Topology

Genesys Surround is deployed entirely in the **customer's Azure tenant** (or a dedicated enterprise AI tenant):

- No shared infrastructure with Microsoft or integration vendors
- Customer controls all data, keys, and model access
- Deployed via **Infrastructure-as-Code** (Bicep/Terraform) for repeatable environments
- Multi-region support for data residency and high availability requirements
