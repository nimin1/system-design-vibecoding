# Load Balancer Diagrams

## Basic Load Balancer Architecture

```mermaid
flowchart LR
    subgraph Clients
        C1[👤 User 1]
        C2[👤 User 2]
        C3[👤 User 3]
        C4[👤 User 4]
    end
    
    LB[⚖️ Load Balancer]
    
    subgraph Servers["Backend Servers"]
        S1[🖧 Server 1]
        S2[🖧 Server 2]
        S3[🖧 Server 3]
    end
    
    C1 --> LB
    C2 --> LB
    C3 --> LB
    C4 --> LB
    
    LB --> S1
    LB --> S2
    LB --> S3
    
    S1 -.->|"Health Check"| LB
    S2 -.->|"Health Check"| LB
    S3 -.->|"Health Check"| LB
```

## Load Balancing Algorithms

### Round Robin
```mermaid
flowchart LR
    LB[Load Balancer]
    
    R1["Request 1"] --> LB
    R2["Request 2"] --> LB
    R3["Request 3"] --> LB
    R4["Request 4"] --> LB
    
    LB -->|"1, 4"| S1[Server 1]
    LB -->|"2"| S2[Server 2]
    LB -->|"3"| S3[Server 3]
```

### Weighted Round Robin
```mermaid
flowchart LR
    LB[Load Balancer]
    
    LB -->|"50% traffic"| S1["Server 1<br/>Weight: 5"]
    LB -->|"30% traffic"| S2["Server 2<br/>Weight: 3"]
    LB -->|"20% traffic"| S3["Server 3<br/>Weight: 2"]
    
    style S1 fill:#90EE90,stroke:#333
    style S2 fill:#87CEEB,stroke:#333
    style S3 fill:#DDA0DD,stroke:#333
```

### Least Connections
```mermaid
flowchart LR
    LB[Load Balancer]
    
    subgraph Before["Current State"]
        S1B["Server 1<br/>Connections: 10"]
        S2B["Server 2<br/>Connections: 3"]
        S3B["Server 3<br/>Connections: 8"]
    end
    
    NewReq["New Request"] --> LB
    LB -->|"Routes to least busy"| S2B
    
    style S2B fill:#90EE90,stroke:#333,stroke-width:3px
```

## Layer 4 vs Layer 7 Load Balancing

```mermaid
flowchart TB
    subgraph L4["Layer 4 (Transport)"]
        direction LR
        L4LB["L4 Load Balancer"]
        L4LB -->|"Based on IP/Port"| L4S1["Server 1"]
        L4LB -->|"Based on IP/Port"| L4S2["Server 2"]
        L4Note["✓ Fast<br/>✓ Simple<br/>✗ No content awareness"]
    end
    
    subgraph L7["Layer 7 (Application)"]
        direction LR
        L7LB["L7 Load Balancer"]
        L7LB -->|"/api/*"| L7S1["API Server"]
        L7LB -->|"/static/*"| L7S2["Static Server"]
        L7LB -->|"/ws/*"| L7S3["WebSocket Server"]
        L7Note["✓ Content-based routing<br/>✓ SSL termination<br/>✗ Higher latency"]
    end
    
    style L4 fill:#e6f3ff,stroke:#333
    style L7 fill:#fff0e6,stroke:#333
```

## Health Checks

```mermaid
sequenceDiagram
    participant LB as Load Balancer
    participant S1 as Server 1 ✅
    participant S2 as Server 2 ✅
    participant S3 as Server 3 ❌
    
    loop Every 10 seconds
        LB->>S1: GET /health
        S1-->>LB: 200 OK
        LB->>S2: GET /health
        S2-->>LB: 200 OK
        LB->>S3: GET /health
        S3--xLB: Timeout/500
    end
    
    Note over LB,S3: Server 3 removed from pool
    
    LB->>S1: Route traffic
    LB->>S2: Route traffic
    Note over S3: No traffic until healthy
```

## High Availability Setup

```mermaid
flowchart TB
    DNS["DNS"]
    
    subgraph Primary["Primary (Active)"]
        LB1["Load Balancer 1<br/>VIP: 10.0.0.1"]
    end
    
    subgraph Secondary["Secondary (Standby)"]
        LB2["Load Balancer 2<br/>VIP: 10.0.0.1"]
    end
    
    DNS --> LB1
    LB1 <-.->|"Heartbeat"| LB2
    
    subgraph Backend
        S1[Server 1]
        S2[Server 2]
        S3[Server 3]
    end
    
    LB1 --> S1
    LB1 --> S2
    LB1 --> S3
    
    LB2 -.->|"Failover"| S1
    LB2 -.->|"Failover"| S2
    LB2 -.->|"Failover"| S3
    
    style Primary fill:#90EE90,stroke:#333
    style Secondary fill:#FFE4B5,stroke:#333
```
