# Distributed Systems Diagrams

## CAP Theorem

```mermaid
flowchart TB
    subgraph CAP["CAP Theorem - Pick 2"]
        C["🔷 Consistency<br/>All nodes see same data"]
        A["🔷 Availability<br/>Every request gets response"]
        P["🔷 Partition Tolerance<br/>System works despite<br/>network failures"]
    end
    
    subgraph Choices["System Choices"]
        CP["CP Systems<br/>MongoDB, HBase, Redis<br/>✓ Consistent, Partition-tolerant<br/>✗ May be unavailable"]
        AP["AP Systems<br/>Cassandra, DynamoDB, CouchDB<br/>✓ Available, Partition-tolerant<br/>✗ May be inconsistent"]
        CA["CA Systems<br/>Traditional RDBMS (single node)<br/>✓ Consistent, Available<br/>✗ No partition tolerance"]
    end
    
    C --- CP
    C --- CA
    A --- AP
    A --- CA
    P --- CP
    P --- AP
    
    style CP fill:#87CEEB,stroke:#333
    style AP fill:#90EE90,stroke:#333
    style CA fill:#DDA0DD,stroke:#333
```

## Message Queue Architecture

```mermaid
flowchart LR
    subgraph Producers
        P1["Producer 1"]
        P2["Producer 2"]
        P3["Producer 3"]
    end
    
    subgraph Queue["Message Queue"]
        direction TB
        Topic["Topic: orders"]
        Q1["Partition 0"]
        Q2["Partition 1"]
        Q3["Partition 2"]
    end
    
    subgraph Consumers["Consumer Group"]
        C1["Consumer 1"]
        C2["Consumer 2"]
        C3["Consumer 3"]
    end
    
    P1 --> Topic
    P2 --> Topic
    P3 --> Topic
    
    Topic --> Q1
    Topic --> Q2
    Topic --> Q3
    
    Q1 --> C1
    Q2 --> C2
    Q3 --> C3
    
    style Queue fill:#ffd93d,stroke:#333
```

## Event-Driven Architecture

```mermaid
flowchart TB
    subgraph Services
        OrderSvc["Order Service"]
        PaymentSvc["Payment Service"]
        InventorySvc["Inventory Service"]
        NotificationSvc["Notification Service"]
    end
    
    EventBus["📨 Event Bus / Message Broker"]
    
    OrderSvc -->|"OrderCreated"| EventBus
    EventBus -->|"Subscribe"| PaymentSvc
    EventBus -->|"Subscribe"| InventorySvc
    EventBus -->|"Subscribe"| NotificationSvc
    
    PaymentSvc -->|"PaymentProcessed"| EventBus
    InventorySvc -->|"InventoryReserved"| EventBus
    
    style EventBus fill:#ff6b6b,stroke:#333,stroke-width:3px
```

## Saga Pattern (Distributed Transactions)

```mermaid
sequenceDiagram
    participant O as Order Service
    participant P as Payment Service
    participant I as Inventory Service
    participant S as Shipping Service
    
    Note over O,S: Happy Path (Choreography)
    O->>P: 1. Process Payment
    P->>I: 2. Reserve Inventory
    I->>S: 3. Create Shipment
    S-->>O: 4. Order Complete ✅
    
    Note over O,S: Compensation (Rollback)
    O->>P: 1. Process Payment ✅
    P->>I: 2. Reserve Inventory ❌ (Failed)
    I-->>P: 3. Compensate: Refund Payment
    P-->>O: 4. Order Failed, Refunded
```

## Consensus Algorithm (Raft)

```mermaid
flowchart TB
    subgraph Cluster["Raft Cluster"]
        Leader["🔵 Leader<br/>Node 1"]
        F1["⚪ Follower<br/>Node 2"]
        F2["⚪ Follower<br/>Node 3"]
        F3["⚪ Follower<br/>Node 4"]
        F4["⚪ Follower<br/>Node 5"]
    end
    
    Client["Client"]
    
    Client -->|"Write Request"| Leader
    Leader -->|"Replicate"| F1
    Leader -->|"Replicate"| F2
    Leader -->|"Replicate"| F3
    Leader -->|"Replicate"| F4
    
    F1 -.->|"ACK"| Leader
    F2 -.->|"ACK"| Leader
    
    Note["Commit when majority (3/5) ACK"]
    
    Leader -->|"Committed"| Client
    
    style Leader fill:#4ecdc4,stroke:#333,stroke-width:3px
```

## Circuit Breaker Pattern

```mermaid
stateDiagram-v2
    [*] --> Closed
    
    Closed --> Open: Failures > Threshold
    Closed --> Closed: Success / Failure < Threshold
    
    Open --> HalfOpen: Timeout expires
    
    HalfOpen --> Closed: Test request succeeds
    HalfOpen --> Open: Test request fails
    
    note right of Closed: Normal operation
    note right of Open: All requests fail fast
    note right of HalfOpen: Allow one test request
```

## Distributed Locking

```mermaid
sequenceDiagram
    participant S1 as Service 1
    participant S2 as Service 2
    participant Redis
    participant Resource
    
    S1->>Redis: SET lock:resource NX EX 30
    Redis-->>S1: OK (acquired)
    
    S2->>Redis: SET lock:resource NX EX 30
    Redis-->>S2: nil (failed - locked)
    
    S1->>Resource: Access resource
    Resource-->>S1: Done
    
    S1->>Redis: DEL lock:resource
    
    S2->>Redis: SET lock:resource NX EX 30
    Redis-->>S2: OK (acquired)
    S2->>Resource: Access resource
```

## Idempotency Key Pattern

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant DB
    
    Note over Client,DB: First Request
    Client->>API: POST /payment<br/>Idempotency-Key: abc123
    API->>DB: Check if abc123 exists
    DB-->>API: Not found
    API->>DB: Process payment + Store result
    API-->>Client: 200 OK, payment_id: xyz
    
    Note over Client,DB: Retry (Network issue)
    Client->>API: POST /payment<br/>Idempotency-Key: abc123
    API->>DB: Check if abc123 exists
    DB-->>API: Found! Return cached result
    API-->>Client: 200 OK, payment_id: xyz (same)
```
