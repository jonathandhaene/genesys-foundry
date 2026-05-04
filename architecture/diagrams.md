# Architecture Diagrams

This document describes the key reference architecture diagrams for Genesys Surround.
Each diagram is described in text to enable rendering in any Markdown viewer or diagram tool (e.g., draw.io, Lucidchart, Mermaid, PowerPoint).

---

## Diagram 1: High-Level Platform Overview

**Purpose**: Show how Genesys Surround sits between the contact center/CRM layer and the AI layer, acting as a system-independent intermediary.

**Description**:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CUSTOMER SYSTEMS (Existing)                         │
│                                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │   Genesys    │  │ Dynamics 365 │  │  Salesforce  │  │  ServiceNow  │   │
│  │  (Voice/Chat)│  │    (CRM)     │  │    (CRM)     │  │   (ITSM)     │   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
└─────────┼─────────────────┼─────────────────┼─────────────────┼────────────┘
          │ API / Events     │ Webhooks        │ Webhooks        │ API
          ▼                  ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                      GENESYS SURROUND PLATFORM                              │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    INTERACTION LAYER                                 │   │
│  │  Channel Adapters · Unified Interaction Model · Event Ingestion     │   │
│  └─────────────────────────────────┬───────────────────────────────────┘   │
│  ┌─────────────────────────────────▼───────────────────────────────────┐   │
│  │                   ORCHESTRATION LAYER                                │   │
│  │  Copilot Agents · Workflow Engine · Event Router · Context Manager  │   │
│  └──────────────────┬──────────────────────────┬────────────────────────┘   │
│  ┌───────────────────▼──────────┐  ┌───────────▼─────────────────────┐   │
│  │       KNOWLEDGE LAYER        │  │          AI LAYER               │   │
│  │  RAG · Index · CRM Data      │  │  GPT-4o · Prompts · Validation  │   │
│  └──────────────────────────────┘  └─────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    GOVERNANCE LAYER                                  │   │
│  │  Identity · Audit · Compliance · PII Masking · Model Governance     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                       │ Agent Companion UI (real-time)
                                       ▼
                              ┌─────────────────┐
                              │   Agent Desktop  │
                              │  (Browser Panel) │
                              └─────────────────┘
```

**Key message**: Genesys Surround sits *between* the existing systems and the AI capabilities. Systems connect via APIs and events. Agents receive AI assistance through the Agent Companion UI.

---

## Diagram 2: Real-Time Agent Assistance Flow

**Purpose**: Show the sequence of events during a live customer interaction.

**Description**:

```
Customer                Genesys             Genesys Surround           Agent Desktop
   │                      │                       │                         │
   │──── Calls/Chats ────▶│                       │                         │
   │                      │─── Interaction ──────▶│                         │
   │                      │    Event               │                         │
   │                      │                  ┌────▼────────────┐            │
   │                      │                  │ Context Loader   │            │
   │                      │                  │ (CRM lookup)     │            │
   │                      │                  └────┬────────────┘            │
   │                      │                       │─── Customer Context ───▶│
   │                      │                       │                         │
   │── Speaks/Types ──────────────────────────────┤                         │
   │                      │                  ┌────▼────────────┐            │
   │                      │                  │ Intent Detection │            │
   │                      │                  │ + Transcription  │            │
   │                      │                  └────┬────────────┘            │
   │                      │                  ┌────▼────────────┐            │
   │                      │                  │  RAG Knowledge   │            │
   │                      │                  │  Retrieval       │            │
   │                      │                  └────┬────────────┘            │
   │                      │                  ┌────▼────────────┐            │
   │                      │                  │  GPT-4o         │            │
   │                      │                  │  Response Gen   │            │
   │                      │                  └────┬────────────┘            │
   │                      │                       │──── AI Suggestion ─────▶│
   │                      │                       │                         │
   │                      │                       │       Agent reviews &   │
   │                      │                       │       responds to       │
   │◀─────────────────────────────────────────────────────── Customer ──────│
   │                      │                       │                         │
   │── Interaction Ends ──────────────────────────┤                         │
   │                      │                  ┌────▼────────────┐            │
   │                      │                  │  Auto-Summary   │            │
   │                      │                  │  + Case Notes   │            │
   │                      │                  └────┬────────────┘            │
   │                      │                       │──── Summary ───────────▶│
```

---

## Diagram 3: Knowledge Layer Architecture

**Purpose**: Show how the knowledge layer aggregates content from multiple enterprise sources and serves AI grounding.

**Description**:

```
Enterprise Knowledge Sources
┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐
│SharePnt │ │SvcNow KB│ │Salesfrc │ │D365 KB  │ │Internal │
│/Teams   │ │         │ │   KB    │ │         │ │  Docs   │
└────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘
     │           │           │           │           │
     └───────────┴───────────┴───────────┴───────────┘
                             │
                    ┌────────▼────────┐
                    │  Content Sync   │
                    │  + Chunking     │
                    │  + Enrichment   │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  AI Embeddings  │
                    │  (text-embed-3) │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │ Azure AI Search │
                    │  Vector + BM25  │
                    │  Hybrid Index   │
                    └────────┬────────┘
                             │
               ┌─────────────┴─────────────┐
               │                           │
      ┌────────▼────────┐        ┌─────────▼────────┐
      │   RAG Engine     │        │  Knowledge API   │
      │  (context inject)│        │  (direct query)  │
      └────────┬────────┘        └──────────────────┘
               │
      ┌────────▼────────┐
      │   GPT-4o        │
      │   (grounded)    │
      └─────────────────┘
```

---

## Diagram 4: Multi-System Integration Pattern

**Purpose**: Show how the platform connects to multiple CRM/ITSM systems without tight coupling.

**Description**:

```
                    ┌──────────────────────────────────┐
                    │    Genesys Surround Platform       │
                    │                                   │
                    │  ┌─────────────────────────────┐  │
                    │  │   Integration Adapter Layer  │  │
                    │  │                             │  │
                    │  │  ┌───────┐  ┌───────────┐  │  │
                    │  │  │Events │  │ REST APIs  │  │  │
                    │  │  └───┬───┘  └─────┬─────┘  │  │
                    │  └──────┼────────────┼─────────┘  │
                    └─────────┼────────────┼────────────┘
                              │            │
         ┌────────────────────┼────────────┼────────────────────┐
         │                    │            │                    │
┌────────▼──────┐  ┌──────────▼──┐  ┌─────▼──────┐  ┌─────────▼──────┐
│   Genesys     │  │  Dynamics   │  │ Salesforce │  │  ServiceNow    │
│               │  │    365      │  │            │  │                │
│ REST APIs     │  │  OData API  │  │ REST API   │  │ REST API       │
│ Webhooks      │  │  Webhooks   │  │ Streaming  │  │ Webhooks       │
│ Notifications │  │  CDC Events │  │ API        │  │ Events         │
└───────────────┘  └─────────────┘  └────────────┘  └────────────────┘
```

**Key message**: Each system is connected via its native API and event mechanism. No system is modified. The platform uses adapters to translate system-specific formats into the Unified Interaction Model.

---

## Diagram 5: Governance and Data Flow

**Purpose**: Show how PII, audit, and access controls flow through the platform.

**Description**:

```
Raw Interaction Data
        │
        ▼
┌────────────────┐
│  PII Detection │  ← Azure AI Language (PII detection)
│  & Masking     │
└───────┬────────┘
        │ Masked/tokenised data only
        ▼
┌────────────────┐
│  Access Check  │  ← Entra ID RBAC + tenant scoping
└───────┬────────┘
        │ Authorised request only
        ▼
┌────────────────┐
│  AI Processing │  ← Prompt + context → GPT-4o → response
└───────┬────────┘
        │
        ├──────────────────────────────┐
        │                             │
        ▼                             ▼
┌────────────────┐          ┌──────────────────┐
│  Agent UI      │          │  Audit Log       │
│  (response)    │          │  (immutable)     │
└────────────────┘          │  - timestamp     │
                            │  - interaction ID │
                            │  - prompt version │
                            │  - sources used  │
                            │  - response hash │
                            └──────────────────┘
```
