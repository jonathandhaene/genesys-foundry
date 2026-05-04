# Scenario: Knowledge Retrieval Across Silos

## Overview

Enterprise knowledge is typically fragmented across multiple systems: SharePoint, ServiceNow KB, Salesforce Knowledge, Zendesk Help Center, internal wikis, policy documents, and more. Agents must search each system separately — often during live interactions — leading to delays, inconsistency, and errors.

Genesys Surround provides a **unified semantic knowledge layer** that indexes all enterprise knowledge sources and makes them searchable through a single, AI-powered interface.

---

## Problem It Solves

| Problem | Traditional State | With Genesys Surround |
|---|---|---|
| Multiple KB systems | Agent searches 3–5 systems per query | One unified search |
| Inconsistent answers | Different sources give different answers | Conflict detection and resolution |
| Stale knowledge | No visibility into outdated articles | Freshness scoring and alerts |
| Access control fragmentation | Some agents can't access some KBs | Unified RBAC respects source system permissions |
| No AI synthesis | Agent reads and synthesises manually | AI generates a grounded answer with sources |

---

## Knowledge Sources Supported

| Source | Type | Native Connector |
|---|---|---|
| SharePoint / OneDrive | Internal docs, policies | Microsoft Graph API |
| Microsoft Teams (Channels) | Team knowledge, announcements | Microsoft Graph API |
| ServiceNow Knowledge Base | IT / ITSM procedures | ServiceNow REST API |
| Salesforce Knowledge | CRM-linked KB articles | Salesforce REST API |
| Dynamics 365 Knowledge | Case-linked KB articles | D365 OData API |
| Zendesk Help Center | Customer-facing + internal | Zendesk API |
| Confluence | Engineering / product docs | Confluence REST API |
| PDF / Office Documents | Policy, procedure, product docs | Azure AI Document Intelligence |
| Web (internal intranet) | HR, ops, policy portals | Configurable web crawler |

---

## How Knowledge Retrieval Works

### Indexing Pipeline (background process)

```
Source Document
      │
      ▼
 Document Loader
 (connector-specific)
      │
      ▼
 Chunking Engine          ← Splits document into 256–512 token chunks
      │                      with overlap for context preservation
      ▼
 Metadata Extraction      ← Source, author, date, language, access group
      │
      ▼
 Embedding Model          ← text-embedding-3-large
      │
      ▼
 Azure AI Search Index    ← Vector + BM25 hybrid index
```

### Query-Time Flow (real-time)

```
Agent Query
    │
    ▼
Query Expansion           ← Rewrites query for better recall
    │
    ▼
Hybrid Search             ← Vector similarity + keyword BM25
    │
    ▼
Access Control Filter     ← Filters results to agent's authorised sources
    │
    ▼
Re-ranking                ← Cross-encoder model refines top results
    │
    ▼
Context Assembly          ← Top N chunks assembled as LLM context
    │
    ▼
GPT-4o Answer Generation  ← Grounded answer with source citations
    │
    ▼
Agent Companion Panel     ← Answer shown with source links
```

---

## Query Examples

### Voice Interaction
Customer asks: "Can I upgrade my plan mid-year without a penalty?"

AI retrieves:
> "Under the enterprise contract terms (Section 6.4), mid-year upgrades are permitted without penalty. Downgrades require 30 days' notice. Source: Enterprise T&C v4.1 (SharePoint)"

### Chat Interaction
Customer asks: "My laptop won't connect to VPN after the Windows update"

AI retrieves:
> "This is a known issue with the October Windows update (KB5031539). Workaround: disable IPv6 on the VPN adapter. Full fix scheduled for the next IT patch cycle (Tuesday). Source: ServiceNow KB0012345"

---

## Search Configuration

| Setting | Default | Notes |
|---|---|---|
| Max sources returned | 5 | Configurable per agent group |
| Minimum confidence threshold | 0.65 | Below this, "no confident answer" is returned |
| Freshness weight | 0.15 | Boosts recently updated content |
| Source preference | All authorised | Configurable per use case or tenant |
| Language filtering | Auto-detect | Matches agent language preference |

---

## Knowledge Gap Reporting

When queries return low-confidence results, the Knowledge Service logs the gap:

```
Gap Record:
- Query: "How to process refund for expired gift card?"
- Top result confidence: 0.41
- Sources searched: 6
- Action: Flagged for Knowledge Manager review
- Date: 2025-10-15
```

Knowledge Managers receive a weekly gap report via the Knowledge Curation Service.
