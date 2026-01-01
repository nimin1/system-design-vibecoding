# 📊 System Design Diagrams Index

A comprehensive catalog of all Mermaid diagrams embedded throughout this repository.

---

## Overview

This repository contains **47 content files** with embedded Mermaid diagrams. All diagrams are rendered natively by GitHub and provide visual explanations of system design concepts.

---

## Diagram Types Used

| Type | Usage | Best For |
|------|-------|----------|
| `flowchart` | Most common | Architecture overviews, data flow, decision trees |
| `sequenceDiagram` | 15+ diagrams | Request/response flows, protocol handshakes |
| `stateDiagram` | 8+ diagrams | State machines, lifecycle management |
| `graph` | Various | Component relationships, dependencies |

---

## Diagrams by Section

### 01 - Foundation (5 files)

| File | Diagram Content |
|------|-----------------|
| `01-client-server-model.md` | Client-server architecture, request-response cycle |
| `02-how-the-internet-works.md` | DNS resolution flow |
| `03-http-and-apis.md` | HTTP request-response model |
| `04-databases-101.md` | SQL vs NoSQL decision flowchart |
| `05-what-happens-when-you-type-url.md` | Complete URL to page journey |

### 02 - Core Concepts (4 files)

| File | Diagram Content |
|------|-----------------|
| `01-latency-and-throughput.md` | Request latency breakdown journey |
| `02-availability-and-reliability.md` | Nines of availability visualization, redundancy patterns |
| `03-scalability-basics.md` | Horizontal vs vertical scaling comparison |
| `05-cap-theorem-simplified.md` | CAP theorem triangle visualization |

### 03 - Building Blocks (7 files)

| File | Diagram Content |
|------|-----------------|
| `01-load-balancers.md` | Load balancer architecture, L4 vs L7 comparison |
| `02-caching.md` | Cache patterns (cache-aside, write-through, write-behind) |
| `03-cdns.md` | CDN architecture and request flow |
| `04-proxies-and-gateways.md` | Forward vs reverse proxy diagrams |
| `05-rate-limiting.md` | Token bucket algorithm visualization |
| `06-consistent-hashing.md` | Hash ring with virtual nodes |
| `07-bloom-filters.md` | Bloom filter bit array and lookup flow |

### 04 - Data Layer (3 files)

| File | Diagram Content |
|------|-----------------|
| `01-database-indexing.md` | Index lookup vs table scan comparison |
| `02-replication.md` | Primary-replica architecture |
| `03-sharding.md` | Sharding architecture with shard key routing |

### 05 - Distributed Systems (6 files)

| File | Diagram Content |
|------|-----------------|
| `01-message-queues.md` | Message queue producer-consumer architecture |
| `02-event-driven.md` | Traditional vs event-driven architecture comparison |
| `03-handling-failures.md` | Circuit breaker state machine |
| `04-consensus.md` | Raft leader election, log replication sequence |
| `05-distributed-transactions.md` | Saga pattern with compensating actions, orchestration vs choreography |
| `06-distributed-locking.md` | Lock problem and solution diagrams |

### 06 - Architecture Patterns (2 files)

| File | Diagram Content |
|------|-----------------|
| `01-monolith-vs-microservices.md` | Monolith vs microservices architecture comparison |
| `02-api-design.md` | REST vs GraphQL vs gRPC comparison |

### 07 - Real-World Designs (14 files)

| File | Diagram Content |
|------|-----------------|
| `01-url-shortener.md` | URL shortener system architecture |
| `02-rate-limiter.md` | Rate limiter architecture and sequence |
| `03-chat-system.md` | Chat system with WebSocket management |
| `04-notification-system.md` | Notification system architecture |
| `05-news-feed.md` | Fan-out architecture, push vs pull model |
| `06-ecommerce.md` | E-commerce services architecture |
| `07-payment-system.md` | Payment flow and state machine |
| `08-video-streaming.md` | Video platform with encoding pipeline |
| `09-search-engine.md` | Search architecture, inverted index flow |
| `10-ride-sharing.md` | Ride matching architecture and sequence |
| `11-distributed-file-storage.md` | File storage with chunking and replication |
| `12-web-crawler.md` | Crawler architecture with URL frontier |
| `13-key-value-store.md` | Distributed KV with consistent hashing |
| `15-metrics-monitoring.md` | Metrics ingestion pipeline, TSDB architecture |

### 08 - Senior Engineer Thinking (6 files)

| File | Diagram Content |
|------|-----------------|
| `02-observability.md` | Three pillars (logs, metrics, traces), distributed trace visualization |
| `05-incident-management.md` | Incident lifecycle, state machine |

---

## Diagram Style Guide

All diagrams in this repository follow these conventions:

### Icons & Emojis
- 📱 Mobile/Client apps
- 💾 Storage/Databases
- 📨 Message queues
- 🔒 Security/Locks
- ✅ Success states
- ❌ Failure states
- ⏱️ Time-related
- 🚨 Alerts

### Color Coding (where supported)
- Green (`#28a745`) - Primary/Success
- Blue (`#17a2b8`) - Replicas/Secondary
- Red - Failure/Error states

### Subgraph Naming
- Use clear, descriptive names
- Include emoji for visual distinction
- Group related components logically

---

## Creating New Diagrams

When adding diagrams to new content:

1. **Choose the right type:**
   - `flowchart` for architecture and data flow
   - `sequenceDiagram` for request/response patterns
   - `stateDiagram` for state machines

2. **Keep it focused:**
   - One concept per diagram
   - 5-10 nodes maximum for clarity

3. **Use consistent styling:**
   - Follow existing emoji conventions
   - Use descriptive node labels

4. **Test rendering:**
   - Preview in GitHub to ensure proper rendering

---

## Verification

To verify all diagrams render correctly:

```bash
# Count files with Mermaid diagrams
find . -name "*.md" -exec grep -l '```mermaid' {} \; | wc -l

# List all files with diagrams
find . -name "*.md" -exec grep -l '```mermaid' {} \;
```

---

*Last updated: December 2024*
