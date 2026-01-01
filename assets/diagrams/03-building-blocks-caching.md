# Caching Architecture Diagrams

## Cache-Aside Pattern (Lazy Loading)

```mermaid
flowchart TB
    App[Application]
    Cache[(Redis Cache)]
    DB[(Database)]
    
    App -->|"1. Check cache"| Cache
    Cache -->|"2a. Cache HIT"| App
    
    Cache -.->|"2b. Cache MISS"| App
    App -->|"3. Query DB"| DB
    DB -->|"4. Return data"| App
    App -->|"5. Update cache"| Cache
    
    style Cache fill:#ff6b6b,stroke:#333
    style DB fill:#4ecdc4,stroke:#333
```

## Cache Hit vs Cache Miss

```mermaid
sequenceDiagram
    participant Client
    participant App as Application
    participant Cache as Redis
    participant DB as Database
    
    Note over Client,DB: Cache HIT (Fast Path)
    Client->>App: GET /user/123
    App->>Cache: GET user:123
    Cache-->>App: ✅ Data found
    App-->>Client: 200 OK (5ms)
    
    Note over Client,DB: Cache MISS (Slow Path)
    Client->>App: GET /user/456
    App->>Cache: GET user:456
    Cache-->>App: ❌ Not found
    App->>DB: SELECT * FROM users
    DB-->>App: User data
    App->>Cache: SET user:456 (TTL 5min)
    App-->>Client: 200 OK (50ms)
```

## Write-Through Cache

```mermaid
flowchart LR
    Client[Client] --> App[Application]
    App -->|"1. Write"| Cache[(Cache)]
    Cache -->|"2. Write"| DB[(Database)]
    DB -->|"3. Confirm"| Cache
    Cache -->|"4. Confirm"| App
    
    style Cache fill:#ffd93d,stroke:#333
```

## Write-Behind (Write-Back) Cache

```mermaid
flowchart LR
    Client[Client] --> App[Application]
    App -->|"1. Write"| Cache[(Cache)]
    Cache -->|"2. ACK (immediate)"| App
    Cache -.->|"3. Async write"| DB[(Database)]
    
    style Cache fill:#ff6b6b,stroke:#333
    
    Note["⚠️ Risk: Data loss if cache fails<br/>✓ Benefit: Very fast writes"]
```

## Multi-Level Caching

```mermaid
flowchart TB
    Client[Client Request]
    
    subgraph L1["L1: Browser Cache"]
        Browser["Local Storage<br/>Session Storage"]
    end
    
    subgraph L2["L2: CDN Cache"]
        CDN["Edge Servers<br/>Static Assets"]
    end
    
    subgraph L3["L3: Application Cache"]
        AppCache["In-Memory Cache<br/>(Local to each server)"]
    end
    
    subgraph L4["L4: Distributed Cache"]
        Redis["Redis Cluster"]
    end
    
    subgraph L5["L5: Database"]
        DB[(Primary DB)]
    end
    
    Client --> L1
    L1 -->|"Miss"| L2
    L2 -->|"Miss"| L3
    L3 -->|"Miss"| L4
    L4 -->|"Miss"| L5
    
    style L1 fill:#90EE90,stroke:#333
    style L2 fill:#87CEEB,stroke:#333
    style L3 fill:#DDA0DD,stroke:#333
    style L4 fill:#FFB347,stroke:#333
    style L5 fill:#F4A460,stroke:#333
```

## Cache Eviction Strategies

```mermaid
flowchart TB
    subgraph LRU["LRU (Least Recently Used)"]
        direction LR
        L1["A (old)"] --> L2["B"] --> L3["C"] --> L4["D (new)"]
        L1 -.->|"Evict"| X1["❌"]
    end
    
    subgraph LFU["LFU (Least Frequently Used)"]
        direction LR
        F1["A: 2 hits"] --> F2["B: 10 hits"] --> F3["C: 1 hit"] --> F4["D: 5 hits"]
        F3 -.->|"Evict"| X2["❌"]
    end
    
    subgraph TTL["TTL (Time To Live)"]
        direction LR
        T1["A: 60s left"] --> T2["B: 30s left"] --> T3["C: 0s left"] --> T4["D: 120s left"]
        T3 -.->|"Evict"| X3["❌"]
    end
```

## Distributed Cache Architecture

```mermaid
flowchart TB
    subgraph AppServers["Application Servers"]
        A1[App 1]
        A2[App 2]
        A3[App 3]
    end
    
    subgraph RedisCluster["Redis Cluster"]
        subgraph Shard1["Shard 0-5460"]
            M1[Master 1]
            R1[Replica 1]
        end
        subgraph Shard2["Shard 5461-10922"]
            M2[Master 2]
            R2[Replica 2]
        end
        subgraph Shard3["Shard 10923-16383"]
            M3[Master 3]
            R3[Replica 3]
        end
    end
    
    A1 --> RedisCluster
    A2 --> RedisCluster
    A3 --> RedisCluster
    
    M1 --> R1
    M2 --> R2
    M3 --> R3
```

## Cache Stampede Prevention

```mermaid
sequenceDiagram
    participant C1 as Client 1
    participant C2 as Client 2
    participant C3 as Client 3
    participant App as Application
    participant Cache as Cache
    participant DB as Database
    
    Note over C1,DB: Cache expires - stampede risk!
    
    C1->>Cache: GET key (miss)
    C2->>Cache: GET key (miss)
    C3->>Cache: GET key (miss)
    
    Note over App: With Locking
    C1->>App: Acquire lock ✅
    C2->>App: Wait for lock 🔒
    C3->>App: Wait for lock 🔒
    
    App->>DB: Query
    DB-->>App: Data
    App->>Cache: SET key
    App->>C1: Return data
    
    Note over C2,C3: Now cache is populated
    C2->>Cache: GET key (hit) ✅
    C3->>Cache: GET key (hit) ✅
```
