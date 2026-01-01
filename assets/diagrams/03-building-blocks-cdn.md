# CDN (Content Delivery Network) Diagrams

## CDN Architecture Overview

```mermaid
flowchart TB
    subgraph Users["Global Users"]
        US["👤 US User"]
        EU["👤 EU User"]
        ASIA["👤 Asia User"]
    end
    
    subgraph CDN["CDN Edge Network"]
        subgraph USEdge["US Edge PoP"]
            US_E1["Edge Server"]
        end
        subgraph EUEdge["EU Edge PoP"]
            EU_E1["Edge Server"]
        end
        subgraph AsiaEdge["Asia Edge PoP"]
            ASIA_E1["Edge Server"]
        end
    end
    
    Origin["🏢 Origin Server<br/>(Your Server)"]
    
    US -->|"~20ms"| US_E1
    EU -->|"~20ms"| EU_E1
    ASIA -->|"~20ms"| ASIA_E1
    
    US_E1 -.->|"Cache Miss: ~100ms"| Origin
    EU_E1 -.->|"Cache Miss: ~150ms"| Origin
    ASIA_E1 -.->|"Cache Miss: ~200ms"| Origin
    
    style USEdge fill:#90EE90,stroke:#333
    style EUEdge fill:#87CEEB,stroke:#333
    style AsiaEdge fill:#DDA0DD,stroke:#333
```

## CDN Request Flow

```mermaid
sequenceDiagram
    participant User
    participant DNS
    participant Edge as CDN Edge
    participant Origin
    
    User->>DNS: Resolve cdn.example.com
    DNS-->>User: Edge IP (nearest PoP)
    
    User->>Edge: GET /image.jpg
    
    alt Cache HIT
        Edge-->>User: Return cached content (5ms)
    else Cache MISS
        Edge->>Origin: Fetch /image.jpg
        Origin-->>Edge: Content + Headers
        Edge->>Edge: Store in cache
        Edge-->>User: Return content (100ms)
    end
```

## Push vs Pull CDN

```mermaid
flowchart TB
    subgraph Push["Push CDN"]
        direction LR
        O1["Origin"] -->|"Upload"| E1["Edge 1"]
        O1 -->|"Upload"| E2["Edge 2"]
        O1 -->|"Upload"| E3["Edge 3"]
        Note1["✓ Content ready immediately<br/>✓ Predictable<br/>✗ Storage costs<br/>✗ Manual updates"]
    end
    
    subgraph Pull["Pull CDN"]
        direction LR
        U1["User"] --> E4["Edge"]
        E4 -->|"First request"| O2["Origin"]
        O2 -->|"Cache + Return"| E4
        E4 --> U1
        Note2["✓ Auto-caching<br/>✓ Storage efficient<br/>✗ First request slow<br/>✗ Cache stampede risk"]
    end
    
    style Push fill:#e6f3ff,stroke:#333
    style Pull fill:#fff0e6,stroke:#333
```

## Cache Control Headers

```mermaid
flowchart LR
    subgraph Headers["Cache-Control Directives"]
        direction TB
        H1["max-age=3600"]
        H2["s-maxage=86400"]
        H3["no-cache"]
        H4["no-store"]
        H5["private"]
        H6["public"]
    end
    
    subgraph Meaning["What They Mean"]
        direction TB
        M1["Browser cache: 1 hour"]
        M2["CDN cache: 24 hours"]
        M3["Revalidate before use"]
        M4["Never cache"]
        M5["User-specific, no CDN"]
        M6["CDN can cache"]
    end
    
    H1 --- M1
    H2 --- M2
    H3 --- M3
    H4 --- M4
    H5 --- M5
    H6 --- M6
```

## CDN for Different Content Types

```mermaid
flowchart TB
    subgraph Static["Static Content"]
        direction LR
        S1["Images"]
        S2["CSS/JS"]
        S3["Fonts"]
        S4["Videos"]
        SConfig["TTL: Long (days/weeks)<br/>Cache-Control: public, max-age=31536000"]
    end
    
    subgraph Dynamic["Semi-Dynamic Content"]
        direction LR
        D1["API Responses"]
        D2["Personalized"]
        D3["Search Results"]
        DConfig["TTL: Short (seconds/minutes)<br/>Cache-Control: private, max-age=60"]
    end
    
    subgraph Never["Never Cache"]
        direction LR
        N1["Auth Tokens"]
        N2["Payment Data"]
        N3["User Sessions"]
        NConfig["Cache-Control: no-store"]
    end
    
    style Static fill:#90EE90,stroke:#333
    style Dynamic fill:#FFE4B5,stroke:#333
    style Never fill:#FFB6C1,stroke:#333
```

## CDN with Origin Shield

```mermaid
flowchart TB
    subgraph Edge["Edge PoPs (Many)"]
        E1["Edge US-West"]
        E2["Edge US-East"]
        E3["Edge EU"]
        E4["Edge Asia"]
    end
    
    Shield["🛡️ Origin Shield<br/>(Single PoP)"]
    Origin["🏢 Origin Server"]
    
    E1 --> Shield
    E2 --> Shield
    E3 --> Shield
    E4 --> Shield
    
    Shield --> Origin
    
    Note["Without Shield: 4 origin requests<br/>With Shield: 1 origin request"]
    
    style Shield fill:#ffd93d,stroke:#333,stroke-width:3px
```

## CDN Invalidation Strategies

```mermaid
flowchart TB
    subgraph Strategies["Invalidation Approaches"]
        direction TB
        
        subgraph Purge["Purge (Immediate)"]
            P1["DELETE /cdn/path/*"]
            P2["All edges clear cache"]
        end
        
        subgraph Version["Versioned URLs"]
            V1["style.v1.css → style.v2.css"]
            V2["Old version expires naturally"]
        end
        
        subgraph TTL["Short TTL"]
            T1["max-age=300"]
            T2["Stale in 5 minutes"]
        end
    end
    
    style Purge fill:#FFB6C1,stroke:#333
    style Version fill:#90EE90,stroke:#333
    style TTL fill:#87CEEB,stroke:#333
```
