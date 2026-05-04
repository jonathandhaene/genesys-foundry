# Architecture Overview

## The "Surround" Philosophy

Genesys Surround is built on a fundamental principle: **AI should not live inside your contact center platform — it should surround it.**

Traditional approaches embed AI features directly into telephony systems or CRM products. This creates vendor lock-in, fragmented knowledge, and duplicated costs. The surround approach instead:

- Positions AI as an **independent platform layer** that connects to all systems via APIs and events
- Centralises knowledge, governance, and model management in **one place**
- Allows the underlying contact center or CRM to be replaced **without losing AI capabilities**

---

## Five-Layer Reference Architecture

The platform is structured into five distinct, independently scalable layers:

### 1. Interaction Layer

The Interaction Layer is the entry point for all customer and agent communications. It captures and normalises signals from every channel:

- **Voice** – telephony events from Genesys (or any compatible platform) via SIP/REST/WebSocket
- **Chat** – web chat, in-app messaging, Microsoft Teams
- **Email** – structured email ingestion with metadata extraction
- **Social & Messaging** – WhatsApp, SMS, Twitter/X, Facebook Messenger
- **CRM Events** – case creation, updates, escalations from D365, Salesforce, ServiceNow, Zendesk

All interactions are normalised into a **Unified Interaction Model (UIM)**: a canonical JSON envelope that is channel-agnostic and system-agnostic. This is the foundation for consistent AI processing regardless of origin.

### 2. Orchestration Layer

The Orchestration Layer is the brain of the platform. It receives normalised interaction events and routes them to the right AI capabilities:

- **Agent Copilot Orchestrator** – manages real-time assistance during live interactions
- **Workflow Engine** – coordinates multi-step AI tasks (e.g., summarise → classify → suggest → log)
- **Event Router** – pub/sub bus connecting interactions to AI services and back to source systems
- **Session Context Manager** – maintains conversation state across turns and channels

Built on **Microsoft Copilot Studio** and **Azure Logic Apps / Durable Functions** for enterprise-grade reliability.

### 3. Knowledge Layer

The Knowledge Layer provides the factual grounding for all AI responses:

- **RAG Engine** – Retrieval-Augmented Generation using Azure AI Search (vector + hybrid)
- **Semantic Index** – unified index across SharePoint, Confluence, ServiceNow KB, Salesforce KB, internal wikis
- **CRM Data Connector** – real-time retrieval of case history, customer profile, SLA status
- **Policy & Procedure Store** – versioned internal procedures with access controls
- **Interaction History** – past conversations indexed for context and personalisation

The knowledge layer ensures AI responses are **grounded in enterprise truth** — not hallucinated from model weights alone.

### 4. AI Layer

The AI Layer contains the models and prompt engineering capabilities:

- **LLM Foundation** – Azure OpenAI (GPT-4o) as the primary reasoning model
- **Embedding Model** – text-embedding-3-large for semantic search
- **Prompt Engineering Framework** – templated, versioned, governed prompts per persona and use case
- **Response Filters** – output validation, toxicity filters, grounding checks
- **Specialised Models** – fine-tuned or purpose-built models for specific domains (e.g., financial services, healthcare)

### 5. Governance Layer

The Governance Layer is not an afterthought — it is a first-class platform citizen:

- **Identity & Access** – Azure AD / Entra ID integration, role-based access, least privilege
- **Data Isolation** – tenant and user-level data boundaries enforced at every layer
- **Audit Log** – immutable log of every AI inference, data access, and agent action
- **Compliance Engine** – configurable rules for GDPR, HIPAA, PCI-DSS, FSI regulations
- **Responsible AI** – content filtering, bias detection, model monitoring
- **PII Detection & Masking** – automatic detection and masking before AI processing

---

## Design Principles

| Principle | Description |
|---|---|
| **System independence** | AI platform connects to systems; it does not depend on any single one |
| **Event-driven** | All integration via events and APIs; no direct database coupling |
| **Loosely coupled** | Each layer can be updated or replaced independently |
| **Privacy by design** | PII handling, data minimisation, purpose limitation built in from the start |
| **Auditability** | Every AI decision is traceable, explainable, and logged |
| **Scalability** | Designed for enterprise scale: multi-tenant, multi-region, high availability |

---

## Technology Stack

| Layer | Technology |
|---|---|
| Orchestration | Microsoft Copilot Studio, Azure Durable Functions, Azure Service Bus |
| Knowledge | Azure AI Search, Azure Blob Storage, Azure Cosmos DB |
| AI Models | Azure OpenAI Service (GPT-4o, text-embedding-3-large) |
| Identity | Microsoft Entra ID, Azure Key Vault |
| Integration | Azure API Management, Azure Event Grid, REST/WebSocket adapters |
| Observability | Azure Monitor, Application Insights, Log Analytics |
| Governance | Microsoft Purview, Azure Policy, Defender for Cloud |

---

> See [`/architecture/diagrams.md`](../architecture/diagrams.md) for detailed diagram descriptions and [`/architecture/layers.md`](../architecture/layers.md) for deep-dive layer documentation.
