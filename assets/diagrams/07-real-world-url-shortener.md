# Real-World Design: URL Shortener

## High-Level Architecture

```mermaid
flowchart TB
    subgraph Clients
        Web["🌐 Web"]
        API["📡 API Clients"]
    end
    
    subgraph Gateway
        LB["Load Balancer"]
        RateLimit["Rate Limiter"]
    end
    
    subgraph Services
        Create["URL Creation Service"]
        Redirect["Redirect Service"]
        Analytics["Analytics Service"]
    end
    
    subgraph Data
        Cache[(Redis Cache)]
        DB[(Database)]
        ClickHouse[(ClickHouse<br/>Analytics)]
    end
    
    Web --> LB
    API --> LB
    LB --> RateLimit
    RateLimit --> Create
    RateLimit --> Redirect
    
    Create --> DB
    Create --> Cache
    Redirect --> Cache
    Cache -.->|"Cache Miss"| DB
    Redirect --> Analytics
    Analytics --> ClickHouse
```

## URL Shortening Flow

```mermaid
sequenceDiagram
    participant User
    participant API as API Server
    participant ID as ID Generator
    participant DB as Database
    participant Cache as Redis
    
    User->>API: POST /shorten {url: "https://very-long-url.com/..."}
    API->>ID: Generate unique ID
    ID-->>API: "abc123" (Base62)
    API->>DB: Store mapping
    DB-->>API: Success
    API->>Cache: Cache mapping
    API-->>User: {"short_url": "https://short.ly/abc123"}
```

## Redirect Flow

```mermaid
sequenceDiagram
    participant User
    participant LB as Load Balancer
    participant Svc as Redirect Service
    participant Cache as Redis
    participant DB as Database
    participant Analytics
    
    User->>LB: GET /abc123
    LB->>Svc: Route request
    Svc->>Cache: GET abc123
    
    alt Cache Hit
        Cache-->>Svc: https://original-url.com
    else Cache Miss
        Svc->>DB: Query by short_code
        DB-->>Svc: https://original-url.com
        Svc->>Cache: SET abc123
    end
    
    Svc-->>User: 301 Redirect to original URL
    Svc--)Analytics: Log click (async)
```

## ID Generation Strategies

```mermaid
flowchart TB
    subgraph Strategy1["Counter + Base62"]
        direction LR
        Counter["Counter: 123456789"]
        Encode["Base62 Encode"]
        Result1["→ 8M0kX"]
        Counter --> Encode --> Result1
    end
    
    subgraph Strategy2["Hash Truncation"]
        direction LR
        URL["Long URL"]
        Hash["MD5/SHA256"]
        Truncate["First 7 chars"]
        Result2["→ 2cf24d"]
        URL --> Hash --> Truncate --> Result2
    end
    
    subgraph Strategy3["Pre-generated Keys"]
        direction LR
        KeyDB["Key Database<br/>(Pre-generated pool)"]
        Claim["Claim unused key"]
        Result3["→ Xk39Mn"]
        KeyDB --> Claim --> Result3
    end
    
    style Strategy1 fill:#90EE90,stroke:#333
    style Strategy2 fill:#87CEEB,stroke:#333
    style Strategy3 fill:#DDA0DD,stroke:#333
```

## Base62 Encoding

```mermaid
flowchart LR
    subgraph Alphabet["Base62 Alphabet (62 chars)"]
        A["0-9 (10)"]
        B["a-z (26)"]
        C["A-Z (26)"]
    end
    
    subgraph Examples
        E1["62^1 = 62 URLs"]
        E2["62^6 = 56.8 billion URLs"]
        E3["62^7 = 3.5 trillion URLs"]
    end
    
    Calculation["7 characters = 3,521,614,606,208 unique URLs<br/>Enough for most use cases"]
```

## Data Model

```mermaid
erDiagram
    URLS {
        string short_code PK "abc123"
        string original_url "https://..."
        uuid created_by FK
        timestamp created_at
        timestamp expires_at
        boolean is_active
    }
    
    USERS {
        uuid user_id PK
        string email
        string api_key
        int rate_limit
    }
    
    CLICKS {
        uuid click_id PK
        string short_code FK
        timestamp clicked_at
        string ip_address
        string user_agent
        string referrer
        string country
    }
    
    USERS ||--o{ URLS : creates
    URLS ||--o{ CLICKS : tracks
```

## Handling Collisions

```mermaid
flowchart TB
    Input["Generate short code"]
    Check{"Code exists<br/>in DB?"}
    Store["Store mapping"]
    Retry["Regenerate with<br/>different seed"]
    
    Input --> Check
    Check -->|"No"| Store
    Check -->|"Yes"| Retry
    Retry --> Check
    
    Note["Collision probability with 7 chars:<br/>~1 in 3.5 trillion per URL"]
```

## Rate Limiting

```mermaid
flowchart TB
    Request["API Request"]
    
    subgraph RateLimiter["Rate Limiter (Redis)"]
        Check["Check: user:123:requests"]
        Limit{"Under limit?"}
        Allow["✅ Allow"]
        Block["❌ 429 Too Many Requests"]
    end
    
    Request --> Check
    Check --> Limit
    Limit -->|"Yes"| Allow
    Limit -->|"No"| Block
    
    subgraph Tiers["Rate Limits"]
        Free["Free: 100/hour"]
        Pro["Pro: 10,000/hour"]
        Enterprise["Enterprise: Unlimited"]
    end
```
